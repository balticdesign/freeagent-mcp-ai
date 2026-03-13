# Tax Knowledge Base — UK Sole Traders

This document defines the tax intelligence layer used by the AI when processing transactions and providing expense advice. It covers HMRC rules applicable to UK sole traders for the 2025/26 tax year.

> **Disclaimer**: This knowledge base provides general guidance. Individual circumstances vary. Always consult a qualified accountant for complex situations.

## 1. Expense Claimability Rules

### The Core Test

HMRC's fundamental rule: an expense must be incurred **"wholly and exclusively"** for business purposes. If an expense has a dual (business and personal) purpose, it can only be claimed if the business element can be **clearly separated**.

### Allowable Expense Categories

#### Office, Property & Equipment
- Computer hardware and software used for business
- Office furniture and equipment
- Stationery, printing, postage
- Phone and broadband (business portion)
- Domain names, hosting, SaaS subscriptions

#### Travel & Vehicle
- Business mileage (simplified: 45p/mile first 10,000, 25p after)
- Parking, tolls, congestion charges (business journeys only)
- Public transport for business travel
- **NOT commuting** — home to regular workplace is never claimable

#### Business Premises (Home Office)
- Simplified flat rate: £10/month (25-50 hrs), £18/month (51-100 hrs), £26/month (101+ hrs)
- Or actual costs apportioned by room/usage

#### Professional Services
- Accountancy fees
- Legal fees (business-related)
- Professional memberships and subscriptions

#### Marketing & Advertising
- Website costs, SEO tools
- Advertising spend
- Business cards, promotional materials

#### Insurance
- Professional indemnity
- Public liability
- Business contents
- **NOT** personal insurance policies

#### Financial
- Bank charges (business account)
- Credit card/PayPal fees on business transactions
- Interest on business loans

#### Training & Development
- Courses that update/maintain existing skills
- **NOT** courses that teach entirely new skills unrelated to current business
- Books and publications related to business

## 2. VAT Treatment Guide

### Standard Rate (20%)
Most goods and services: software subscriptions, equipment, professional services, advertising.

### Exempt
Insurance premiums, some financial services, some education/training.

### Zero-Rated
Books and e-books, some food items, children's clothing.

### Out of Scope
Bank transfers, wages, drawings, loan repayments, personal expenses.

### For Non-VAT-Registered Sole Traders
- Leave VAT as "Auto" or "Out of Scope" in FreeAgent
- VAT paid on purchases cannot be reclaimed
- Include gross amount as the expense

### For VAT-Registered Sole Traders
- Reclaim VAT on business purchases
- FreeAgent auto-calculates when category is set correctly
- Cannot reclaim VAT on: business entertaining, car purchases (in most cases)

## 3. Mixed-Use Expense Apportionment

### Vehicle Expenses
**Simplified method (recommended for sole traders):**
- 45p per business mile (first 10,000 miles in tax year)
- 25p per business mile (after 10,000)
- Keep a mileage log
- Cannot claim any other vehicle costs if using simplified method

**Actual costs method:**
- Claim business percentage of all vehicle costs
- Must track total miles vs business miles
- **Cannot switch** between methods for the same vehicle

### Phone & Broadband
- Dedicated business line: 100% claimable
- Shared personal/business: apportion by usage
- Typical defensible split: 25-50% business depending on role

### Computer Equipment & Software
- Used solely for business: 100%
- Shared use: apportion honestly
- If business is primary use: 60-80% is typically defensible

### Music Production Software/Hardware (Flowr Context)
- If user has a published app generating/intended to generate revenue
- Production tools directly used for app content: claimable
- If also used personally: apportion (50/50 or 60/40 typical)
- Keep evidence of which content/samples feed into the commercial product

## 4. FreeAgent Category Mappings

| FreeAgent Category | HMRC SA103 Box | Default VAT | Mixed Use? | Notes |
|---|---|---|---|---|
| Accommodation and Meals | Travel costs | 20% | Rarely | Business travel only, never entertaining |
| Advertising and Promotion | Advertising | 20% | No | Marketing spend |
| Bank Charges | Bank/finance charges | Exempt | No | Business account fees |
| Business Entertaining | NOT ALLOWABLE | 20% | N/A | Cannot claim, even if business-related |
| Car Insurance | Car/van costs | Exempt | Yes | Business % only |
| Computer Equipment | Office equipment | 20% | Sometimes | Capital allowance if >£1,000 |
| Computer Software | Office costs | 20% | Sometimes | SaaS subs, licences |
| Consulting and Professional Fees | Accountancy fees | 20% | No | Accountant, legal |
| General Admin | Office costs | 20% | Sometimes | Stationery, printing |
| Insurance | Insurance | Exempt | No | PI, public liability only |
| Motor Expenses | Car/van costs | 20% | Yes | Fuel, repairs (actual method) |
| Postage and Delivery | Office costs | 0-20% | No | Royal Mail is zero-rated |
| Rent, Rates and Power | Premises costs | Varies | Yes | Home office portion |
| Staff Costs | Employee costs | Varies | No | Subcontractors, freelancers |
| Subscriptions | Other allowable | 20% | Sometimes | Professional bodies, trade journals |
| Telephone and Internet | Phone/broadband | 20% | Yes | Business portion |
| Training | Other allowable | Exempt | No | Must maintain existing skills |
| Travel | Travel costs | Varies | No | Business journeys only |

## 5. Capital vs Revenue

### Revenue Expenses (claim in full, current year)
- Items expected to last less than 2 years
- Repairs and maintenance (restoring, not improving)
- Software subscriptions (monthly/annual)

### Capital Expenses (claim via capital allowances)
- Items expected to last 2+ years
- Computers, vehicles, significant equipment
- Annual Investment Allowance: 100% in year of purchase (up to £1M)
- Small items under £1,000 can typically be treated as revenue

## 6. Merchant Pattern Defaults

Common UK merchants and their typical categorisation:

| Pattern | Category | VAT | Type |
|---|---|---|---|
| AMAZON.CO.UK | Varies — ask user | 20% | Could be business or personal |
| MICROSOFT | Computer Software | 20% | Often business (365, Azure) |
| APPLE.COM | Computer Software | 20% | Check if business tool or personal |
| GOOGLE | Advertising/Software | 20% | Ads vs Workspace vs personal |
| HISCOX | Insurance | Exempt | Business insurance |
| KINSTA | Hosting | 20% | Business (web hosting) |
| MANAGEWP | Computer Software | 20% | Business (WordPress management) |
| BRIGHTLOCAL | Computer Software | 20% | Business (SEO tool) |
| PAYPAL FEE | Bank Charges | Exempt | Business transaction fee |
| SPLICE | Computer Software | 20% | Business if music app exists |
| COSTA/STARBUCKS | NOT claimable | 20% | Personal (unless client meeting) |
| BP/SHELL/MFG | Motor Expenses | 20% | Partial business (apportion) |
| ESURE/AVIVA | Car Insurance | Exempt | Partial business (apportion) |
| VODAFONE/EE/O2 | Telephone | 20% | Partial business (apportion) |

## 7. Confidence Scoring

When auto-categorising transactions, use this framework:

| Confidence | Criteria | Action |
|---|---|---|
| 0.9 - 1.0 | Exact merchant match + clear category + no ambiguity | Auto-explain, mark for approval |
| 0.7 - 0.89 | Strong merchant match but could be personal | Auto-explain, mark for approval with note |
| 0.5 - 0.69 | Partial match, needs context | Flag for user review |
| 0.0 - 0.49 | Unknown merchant or ambiguous | Ask user |

## 8. Tax Calculation Reference (2025/26)

### Income Tax Bands (England/Wales/NI)
- Personal Allowance: £12,570 (reduces by £1 for every £2 over £100,000)
- Basic Rate: 20% on £12,571 – £50,270
- Higher Rate: 40% on £50,271 – £125,140
- Additional Rate: 45% on £125,141+

### Class 4 National Insurance
- Lower Profits Limit: £12,570
- Rate: 6% on profits between £12,570 – £50,270
- Rate: 2% on profits above £50,270

### Class 2 National Insurance
- £3.45/week if profits above Small Profits Threshold (£6,725)
- Collected via Self Assessment

### Payments on Account
- Due 31 January and 31 July
- Each payment = 50% of previous year's tax bill
- Not required if previous year's tax < £1,000

---

*Last updated: March 2026 — Tax year 2025/26 rules*
*This knowledge base should be reviewed and updated annually when new tax year rates are announced.*
