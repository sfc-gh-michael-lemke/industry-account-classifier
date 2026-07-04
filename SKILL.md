---
name: industry-account-classifier
description: "Classify a company into the correct Snowflake RevOps Industry and Subindustry from a plain-text description. Use when: classifying an account, determining industry for a company, what industry is a company, account classify, industry classify, classify company, what industry is, tag industry, assign industry, industry subindustry lookup. Input is any free-text company description; output is Industry || Subindustry from the supported taxonomy."
---

# Industry Account Classifier

Classifies a company into the correct Industry and Subindustry from the Snowflake RevOps taxonomy using a plain-text description. Returns a single `Industry || Subindustry` value from the 56 supported options, plus the responsible Industry Leader and a confidence note.

## Supported Taxonomy

| Code | Industry | Subindustry | Industry Leader |
|------|----------|-------------|----------------|
| 1.1 | Public Sector | Federal & National Government | Glenn Powell |
| 1.2 | Public Sector | State & Local Government | Glenn Powell |
| 1.3 | Public Sector | K-12 & Higher Education | Glenn Powell |
| 2.1 | Healthcare & Life Sciences | Healthcare Providers | Jesse Cugliotta |
| 2.2 | Healthcare & Life Sciences | Life Sciences | Jesse Cugliotta |
| 2.3 | Healthcare & Life Sciences | Health Insurance Payers | Jesse Cugliotta |
| 2.4 | Healthcare & Life Sciences | Health Tech | Jesse Cugliotta |
| 2.5 | Healthcare & Life Sciences | Life Sciences Tech | Jesse Cugliotta |
| 2.6 | Healthcare & Life Sciences | HCLS Other | Jesse Cugliotta |
| 3.1 | Financial Services | Global Banks | Rinesh Patel |
| 3.2 | Financial Services | Regional Commercial & Retail Banks | Rinesh Patel |
| 3.3 | Financial Services | Wealth & Asset Management | Rinesh Patel |
| 3.4 | Financial Services | Insurance | Rinesh Patel |
| 3.5 | Financial Services | Fintech & Payments | Rinesh Patel |
| 3.6 | Financial Services | FinServ Data Providers | Rinesh Patel |
| 3.7 | Financial Services | Exchanges, Clearing & Services | Rinesh Patel |
| 4.1 | Media & Entertainment | Media & Streaming | Dennis Bucheim |
| 4.2 | Media & Entertainment | Music | Dennis Bucheim |
| 4.3 | Media & Entertainment | Sports & Betting | Dennis Bucheim |
| 4.4 | Media & Entertainment | Video & Online Gaming | Dennis Bucheim |
| 4.5 | Media & Entertainment | Agencies | Dennis Bucheim |
| 4.6 | Media & Entertainment | AdTech & MarTech | Dennis Bucheim |
| 4.7 | Media & Entertainment | Entertainment Other | Dennis Bucheim |
| 5.1 | Telecom | Network Service Provider | Dennis Bucheim |
| 5.2 | Telecom | Equipment & Infrastructure Provider | Dennis Bucheim |
| 5.3 | Telecom | Digital and Service Layer Provider | Dennis Bucheim |
| 6.1 | Retail & Consumer Goods | Retail, General Merchandising | Paul Winsor |
| 6.2 | Retail & Consumer Goods | Brands, CPG | Paul Winsor |
| 6.3 | Retail & Consumer Goods | Brands, Hardlines & Softlines | Paul Winsor |
| 6.4 | Retail & Consumer Goods | Retail, Specialty | Paul Winsor |
| 6.5 | Retail & Consumer Goods | Pure-play eCommerce | Paul Winsor |
| 6.6 | Retail & Consumer Goods | Technology, Services, & Data | Paul Winsor |
| 7.1 | Travel & Hospitality | QSR, Restaurants & Food Service | Paul Winsor |
| 7.2 | Travel & Hospitality | Travel Tech, OTAs and TMCs | Paul Winsor |
| 7.3 | Travel & Hospitality | Airlines, Car, Rail, Cruise, Bus | Paul Winsor |
| 7.4 | Travel & Hospitality | Hotels and Accommodations | Paul Winsor |
| 7.5 | Travel & Hospitality | Other Travel and Hospitality | Paul Winsor |
| 7.6 | Travel & Hospitality | Activities, Parks, Attractions | Paul Winsor |
| 8.1 | Manufacturing & Industrial | Technology Hardware | Tim Long |
| 8.2 | Manufacturing & Industrial | Automotive | Tim Long |
| 8.3 | Manufacturing & Industrial | Aerospace & Defense | Tim Long |
| 8.4 | Manufacturing & Industrial | Industrial Goods | Tim Long |
| 8.5 | Manufacturing & Industrial | Agriculture | Tim Long |
| 8.6 | Manufacturing & Industrial | Logistics, Transportation & Supply Chain | Tim Long |
| 8.7 | Manufacturing & Industrial | Power & Utilities | Tim Long |
| 8.8 | Manufacturing & Industrial | Oil & Gas, Chemicals, and Mining | Tim Long |
| 8.9 | Manufacturing & Industrial | Construction | Tim Long |
| 9.1 | Technology | Software - B2B | Prabhath Nanisetty |
| 9.2 | Technology | Software - B2C | Prabhath Nanisetty |
| 9.3 | Technology | Technology Development Services | Prabhath Nanisetty |
| 10.1 | Consulting & Professional Services | Accounting | Consulting |
| 10.2 | Consulting & Professional Services | Consulting | Consulting |
| 10.3 | Consulting & Professional Services | Legal | Consulting |
| 10.4 | Consulting & Professional Services | Professional Services | Consulting |
| 10.5 | Consulting & Professional Services | Nonprofit Organization | Consulting |
| 10.6 | Consulting & Professional Services | Other Private Service | Consulting |

## Classification Rules

Apply these rules in order — first match wins:

1. **Classify by what the company primarily sells, not who it sells to.**
   A software company selling to hospitals is Technology, not Healthcare. A manufacturer selling to automakers is Manufacturing & Industrial, not Automotive (unless it makes vehicles/vehicle components).

2. **Manufacturers, processors, and distributors → Manufacturing & Industrial**
   Food processors, chemical manufacturers, industrial goods producers, construction firms, mining companies, logistics providers, utility operators.

3. **Software companies → Technology / Software-B2B or Software-B2C**
   - Software-B2B: enterprise, SaaS, developer tools, B2B platforms
   - Software-B2C: consumer apps, consumer subscriptions, gaming
   - Exception: if the company is an *exclusively fintech* payments processor → Financial Services / Fintech & Payments

4. **Fintech & payment gateway companies → Financial Services / Fintech & Payments**
   Payment processors, card issuers, BNPL lenders, digital wallets, point-of-sale finance.

5. **Tax/compliance/RegTech software → Technology / Software-B2B** (not Financial Services)

6. **Investment funds, REITs, asset managers → Financial Services / Wealth & Asset Management**

7. **Credit unions, community banks, regional banks → Financial Services / Regional Commercial & Retail Banks**

8. **Nonprofits, charities, foundations → Consulting & Professional Services / Nonprofit Organization** (NOT Healthcare Providers, even if the nonprofit funds medical research)

9. **Law firms → Consulting & Professional Services / Legal**

10. **Advertising agencies → Media & Entertainment / Agencies**

11. **Wholesale distributors** whose primary activity is logistics/distribution → Manufacturing & Industrial / Logistics, Transportation & Supply Chain

12. **CPG brands and manufacturers** → Retail & Consumer Goods / Brands, CPG (not Retail)

13. **Medical device companies** → Healthcare & Life Sciences / Life Sciences (not Health Tech)

14. **Digital health platforms and health IT software** → Healthcare & Life Sciences / Health Tech

15. **Government agencies and statutory boards** → Public Sector (Federal, State/Local, or K-12 depending on level and mission)

## Workflow

### Step 1: Receive Input

Accept input in any of these forms:
- A free-text paragraph describing the company
- An account name with a brief description
- Multiple companies at once (classify each separately)

**If no description is provided**, ask: "Please provide a description of the company — what it does, who it sells to, and how it makes revenue."

### Step 2: Classify

Apply the classification rules above to select exactly one value from the supported taxonomy. For each company:

1. Identify the **primary revenue-generating activity** (not a secondary or aspirational activity)
2. Apply the rules in order to determine Industry first, then Subindustry
3. Select the single best-fit `Industry || Subindustry` from the table above
4. Note confidence: **High** (clear fit), **Medium** (defensible but ambiguous), **Low** (insufficient description)

### Step 3: Output

For a single company, output:

```
**Classification:** Industry || Subindustry
**Code:** X.X
**Industry Leader:** Name (email@snowflake.com)
**Confidence:** High / Medium / Low
**Reasoning:** One sentence explaining why this classification was chosen.
```

For multiple companies, output a table:

| Account Name | Industry \|\| Subindustry | Code | Industry Leader | Confidence |
|-------------|--------------------------|------|----------------|------------|

Always include a reasoning note below the table for any Medium or Low confidence classifications.

## Stopping Points

None — runs end-to-end. If a description is missing or ambiguous, ask for clarification before classifying.

## Examples

**Input:** "Salesforce provides cloud-based CRM software for enterprise sales, marketing, and service teams."
**Output:** Technology || Software - B2B (9.1) — Prabhath Nanisetty — High confidence. Enterprise SaaS CRM is a clear B2B software product.

**Input:** "Stripe is an internet infrastructure company that helps businesses accept payments online."
**Output:** Financial Services || Fintech & Payments (3.5) — Rinesh Patel — High confidence. Core product is payment processing infrastructure.

**Input:** "Cargill processes and distributes agricultural commodities including grains, oilseeds, and animal feed."
**Output:** Manufacturing & Industrial || Agriculture (8.5) — Tim Long — High confidence. Grain processing and commodity distribution is agricultural manufacturing.

**Input:** "The American Red Cross provides disaster relief, blood services, and humanitarian aid."
**Output:** Consulting & Professional Services || Nonprofit Organization (10.5) — Consulting — High confidence. Humanitarian nonprofit; does not provide clinical healthcare directly.
