# Project Specification — FreeAgent MCP Tax Advisor Edition

**Author:** Dan Cregin / Baltic Design
**Version:** 0.1.0
**Date:** March 2026

## 1. Executive Summary

This project enhances the existing FreeAgent MCP server with AI-powered tax advisory capabilities. The server works entirely within the Claude chat interface — no separate portal or web app is required.

The project forks StupidCodeFactory/freeagent-mcp (TypeScript, Node.js 24+, published on npm) and extends it with three new capability layers:

1. **Intelligent transaction reconciliation** — pattern matching, confidence scoring, PayPal chain detection
2. **UK sole trader tax advisory** — HMRC rules, claimability assessment, apportionment guidance
3. **Business context awareness** — tailored advice based on your company profile and history

## 2. How It Works — No Portal Needed

### One-Time Setup

1. User registers an app on the FreeAgent Developer Dashboard (free)
2. User adds the MCP server config to Claude Desktop
3. On first use, the server provides an OAuth URL — user clicks, logs into FreeAgent, approves
4. Tokens are stored and auto-refreshed (refresh tokens last ~20 years). Setup is done.

### Day-to-Day Usage

Once connected, the user simply talks to Claude:

```
User: "Show me my unexplained transactions from this month"
Claude: [calls get_unexplained_transactions] Found 23 unexplained...

User: "Can I claim my Splice subscription as a business expense?"
Claude: [calls assess_expense_claimability] Checks FreeAgent data,
        knows about Flowr on Play Store, applies HMRC rules...

User: "Auto-categorise the easy ones and flag the rest"
Claude: [calls batch_categorise_unexplained] Processes 23 transactions,
        auto-explains 17 high-confidence, flags 6 for review...
```

## 3. Architecture

### Layer 1: FreeAgent API Client (Existing)

Inherited from the forked codebase. Handles OAuth2 authentication, token refresh, rate limiting (120 req/min, 3600 req/hr), and raw API calls to all FreeAgent v2 endpoints.

### Layer 2: MCP Tool Definitions (Existing + Extended)

The existing server exposes tools for invoices, contacts, bills, bank transactions, explanations, projects, timeslips, and expenses. We extend this with new tools (see Section 4).

### Layer 3: Tax Intelligence Engine (New)

Structured tax knowledge that augments LLM reasoning:
- UK sole trader expense category mapping with HMRC guidance per category
- VAT treatment rules (standard rate, exempt, zero-rated, out of scope)
- Apportionment logic for mixed-use expenses
- Capital allowance vs revenue expense determination
- Merchant pattern database mapping common merchants to expense categories

### Layer 4: Business Context Awareness (New)

Pulls context from FreeAgent's Company endpoint to understand the user's business type, VAT status, and accounting periods. This context is injected so tax advice is tailored to the specific user.

> The LLM sits above all four layers. It receives the tools, tax knowledge, and business context — and applies its own reasoning. The MCP server never makes tax decisions autonomously.

## 4. New MCP Tools

### 4.1 assess_expense_claimability

**Purpose:** Evaluate whether a transaction can be claimed as a business expense under HMRC rules.

**Input:**
| Parameter | Type | Description |
|---|---|---|
| transaction_id | string (opt) | FreeAgent bank transaction ID |
| description | string | Merchant name or transaction description |
| amount | number | Transaction amount in GBP |
| business_context | string (opt) | Additional context (e.g. "I have a music app") |

**Output:**
| Field | Description |
|---|---|
| claimable | 'yes' \| 'partial' \| 'unlikely' \| 'no' |
| confidence | 0.0–1.0 score |
| suggested_category | FreeAgent category URI |
| vat_treatment | 'TAXABLE' \| 'EXEMPT' \| 'OUT_OF_SCOPE' with rate |
| apportionment | Suggested business use % if mixed-use |
| reasoning | Plain English explanation citing HMRC rules |
| hmrc_risk | 'low' \| 'medium' \| 'high' |
| notes | Practical tips (e.g. "keep evidence of business use") |

### 4.2 batch_categorise_unexplained

**Purpose:** Pull all unexplained transactions, run through tax intelligence, return categorised results with confidence scores.

- Fetches transactions using the `view=unexplained` filter
- Runs merchant pattern matching first (fast, high confidence)
- Falls through to LLM-assisted categorisation for unmatched
- Returns: `auto_explained` (>0.9), `needs_review` (0.5–0.9), `unknown` (<0.5)
- Optionally auto-applies via `create_bank_transaction_explanation`
- Marks auto-applied as "for_approval" so user can verify in FreeAgent

### 4.3 match_paypal_chain

**Purpose:** Solve the PayPal currency conversion problem by identifying groups of related transactions.

- Scans unexplained transactions for PayPal patterns
- Groups by matching amounts (accounting for conversion), dates (±3 days), and description patterns
- Creates primary explanation on PayPal payment, transfer explanations for balancing entries
- Handles fee component as Bank/Finance Charges

### 4.4 suggest_apportionment

**Purpose:** Calculate defensible business/personal splits for mixed-use expenses.

- Takes expense category (car, phone, home office) and usage data
- Returns suggested split with HMRC-aligned reasoning
- References simplified expenses rates where applicable
- Stores ratios for consistent future application

### 4.5 tax_position_summary

**Purpose:** Year-to-date financial position with estimated tax liability.

- Pulls P&L from FreeAgent's Profit & Loss endpoint
- Calculates estimated Income Tax + Class 4 NICs
- Shows payments on account status
- Compares spending vs tax reserve allocation
- Flags under/over-funded reserves

### 4.6 learn_merchant_pattern

**Purpose:** Record merchant→category mappings for future auto-matching.

- Extracts normalised merchant name from transaction description
- Stores mapping: pattern → category + VAT treatment + business/personal/partial
- Persists locally as JSON (~/.freeagent-mcp/patterns.json)
- User can review via `list_patterns` tool

## 5. Implementation Plan

### Phase 1: Fork & Foundation (Week 1–2)
- Fork repository and restructure codebase
- Set up development environment with FreeAgent sandbox
- Add merchant pattern storage (JSON-based)
- Implement `learn_merchant_pattern` and `list_patterns`
- Write initial test suite

### Phase 2: Intelligent Reconciliation (Week 3–4)
- Implement `batch_categorise_unexplained` with pattern matching
- Implement `match_paypal_chain` with PayPal-specific heuristics
- Add confidence scoring system
- Build "marked for approval" workflow
- Test against real transaction patterns

### Phase 3: Tax Advisory Layer (Week 5–6)
- Build tax knowledge base (expense categories, VAT rules, HMRC guidance)
- Implement `assess_expense_claimability`
- Implement `suggest_apportionment` with simplified expenses
- Implement `tax_position_summary` from P&L endpoint
- Add business context injection from Company endpoint

### Phase 4: Polish & Publish (Week 7–8)
- Comprehensive README with UK sole trader examples
- MCPB desktop extension packaging
- npm package publication
- Submit to awesome-mcp-servers list
- Blog post / LinkedIn article

## 6. Technical Requirements

| Requirement | Detail |
|---|---|
| Runtime | Node.js 24 LTS or later |
| Language | TypeScript |
| MCP SDK | @modelcontextprotocol/sdk (latest) |
| FreeAgent API | v2 REST API, JSON, OAuth2 |
| Rate Limits | 120 req/min, 3600/hr — batch operations must respect |
| Token Storage | AES-256 encrypted local file, auto-refresh |
| Pattern Storage | Local JSON (~/.freeagent-mcp/patterns.json) |
| MCP Clients | Claude Desktop, Claude.ai, Claude Code, VS Code |
| Testing | Vitest, FreeAgent Sandbox for integration tests |

## 7. Disclaimers

- Provides guidance based on general HMRC rules for UK sole traders
- NOT a substitute for professional accounting advice
- All auto-applied explanations marked "for approval" in FreeAgent
- System flags uncertainty and recommends professional advice when appropriate
- Tax knowledge base reflects 2025/26 rules and needs annual updates
- Does not file tax returns or submit data to HMRC
