# FreeAgent MCP Server — AI Tax Advisor Edition

> A fork of [StupidCodeFactory/freeagent-mcp](https://github.com/StupidCodeFactory/freeagent-mcp), enhanced with AI-powered tax advisory capabilities for UK sole traders.

## What This Does

This MCP server connects Claude (or any MCP-compatible AI) directly to your FreeAgent accounting data. But unlike a basic API wrapper, it adds an **intelligent tax advisory layer** that:

- **Auto-categorises unexplained transactions** with confidence scoring
- **Matches PayPal currency conversion chains** (the ones FreeAgent can't handle)
- **Advises whether expenses are claimable** under HMRC rules, tailored to your business
- **Suggests apportionment** for mixed business/personal expenses
- **Estimates your tax position** in real-time from your P&L data

Everything works inside the chat — no separate portal or web app needed.

## Who Is This For?

UK sole traders and freelancers using FreeAgent who want:
- Making Tax Digital compliance without the manual grind
- Intelligent expense categorisation that learns your patterns
- Tax advice contextualised to their specific business
- Their 100+ unexplained transactions dealt with in minutes, not hours

## Quick Example

```
You: "I have 34 unexplained transactions. Can you sort them out?"

Claude: I can auto-categorise 22 with high confidence (£891.20).
        8 need your review (£289.14), and 4 are PayPal chains
        I can match up (£67.49). Want me to process the easy ones?

You: "Yes. Also, can I claim my Splice subscription?"

Claude: You have Flowr on the Play Store, so music production
        software is claimable under 'Computer Software'. VAT at
        20% reclaimable if registered. HMRC risk: Low — you have
        a published commercial product evidencing business use.
```

## New Tools (Tax Advisor Edition)

| Tool | Description |
|------|-------------|
| `assess_expense_claimability` | Evaluate if a transaction is claimable under HMRC rules |
| `batch_categorise_unexplained` | Pull unexplained transactions, categorise with confidence scores |
| `match_paypal_chain` | Match PayPal payment + credit card deposit + conversion fee chains |
| `suggest_apportionment` | Calculate defensible business/personal splits for mixed-use expenses |
| `tax_position_summary` | Year-to-date P&L with estimated tax liability |
| `learn_merchant_pattern` | Record merchant→category mappings for future auto-matching |

## Inherited Tools (from upstream)

Invoices, contacts, bills, bank transactions, explanations, projects, timeslips, expenses, and more. See [upstream documentation](https://github.com/StupidCodeFactory/freeagent-mcp) for the full list.

## Prerequisites

- Node.js 24 LTS or later
- A FreeAgent account with API access
- FreeAgent API credentials (Client ID and Secret)

## Installation

### From npm (coming soon)

```bash
npm install -g @balticdesign/freeagent-mcp-tax-advisor
```

### From source

```bash
git clone https://github.com/balticdesign/freeagent-mcp-ai.git
cd freeagent-mcp-ai
npm install
npm run build
```

## Configuration

### 1. Get FreeAgent API Credentials

1. Log in to your FreeAgent account
2. Go to **Settings** → **Integrations** → **Developer Dashboard**
3. Create a new app to get your Client ID and Client Secret
4. Set the redirect URI to `http://localhost:3000/callback`

### 2. Set Environment Variables

```bash
export FREEAGENT_CLIENT_ID="your_client_id"
export FREEAGENT_CLIENT_SECRET="your_client_secret"
export FREEAGENT_REDIRECT_URI="http://localhost:3000/callback"
export FREEAGENT_ENVIRONMENT="sandbox"  # or "production"
```

### 3. Connect to Claude Desktop

Add to your Claude Desktop config:

```json
{
  "mcpServers": {
    "freeagent": {
      "command": "npx",
      "args": ["@balticdesign/freeagent-mcp-tax-advisor"],
      "env": {
        "FREEAGENT_CLIENT_ID": "your_client_id",
        "FREEAGENT_CLIENT_SECRET": "your_client_secret",
        "FREEAGENT_REDIRECT_URI": "http://localhost:3000/callback",
        "FREEAGENT_ENVIRONMENT": "production"
      }
    }
  }
}
```

On first use, the server provides an OAuth URL. Visit it to authorise. Tokens auto-refresh after that.

## Architecture

```
┌─────────────────────────────────────────────┐
│  Claude / LLM                               │
│  (Reasoning, tax advice, user interaction)  │
├─────────────────────────────────────────────┤
│  Layer 4: Business Context Awareness        │
│  (Company data, VAT status, business type)  │
├─────────────────────────────────────────────┤
│  Layer 3: Tax Intelligence Engine           │
│  (HMRC rules, category mappings, VAT)       │
├─────────────────────────────────────────────┤
│  Layer 2: MCP Tools (Existing + New)        │
│  (Invoices, transactions, explanations...)  │
├─────────────────────────────────────────────┤
│  Layer 1: FreeAgent API Client              │
│  (OAuth2, rate limiting, REST calls)        │
└─────────────────────────────────────────────┘
```

## Important Disclaimers

- This tool provides guidance based on general HMRC rules for UK sole traders
- It is **not a substitute for professional accounting advice**
- All auto-applied explanations are marked "for approval" in FreeAgent
- The system flags uncertainty and recommends professional advice when appropriate
- Tax knowledge reflects 2025/26 rules and needs annual updates

## Roadmap

- [x] Fork and restructure codebase
- [ ] Merchant pattern matching and storage
- [ ] Batch categorisation with confidence scoring
- [ ] PayPal chain matching
- [ ] Tax claimability assessment
- [ ] Apportionment calculator
- [ ] Tax position summary
- [ ] MCPB desktop extension packaging
- [ ] npm publication

## Contributing

Contributions welcome! Particularly:
- Additional merchant→category pattern mappings
- HMRC rule updates for new tax years
- Support for additional business types beyond sole trader
- Improved PayPal/Stripe transaction chain detection

## Credits

- **Upstream**: [StupidCodeFactory/freeagent-mcp](https://github.com/StupidCodeFactory/freeagent-mcp)
- **Tax Advisor Edition**: [Baltic Design](https://balticdesign.co.uk) — Dan Cregin

## License

MIT
