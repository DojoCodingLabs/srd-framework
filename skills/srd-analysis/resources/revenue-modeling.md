# Revenue Modeling Methodology

## Core Principle

SRD is financially grounded. Every feature, persona, and journey traces back to a dollar amount. Revenue modeling is the first step — it sets the target that constrains everything else.

## Setting the Revenue Target

If the user provides a target (e.g., "$50K MRR"), use it directly.

If not, estimate based on project type:

| Project Type | Typical First-Year Target | Reasoning |
|-------------|--------------------------|-----------|
| Solo dev tool / utility | $5K-15K MRR | Small market, low price point, high competition |
| SaaS product (B2B) | $30K-100K MRR | Higher price points, longer sales cycles |
| SaaS product (B2C) | $20K-75K MRR | Volume-based, lower price points |
| Marketplace | $50K-200K MRR | Two-sided, transaction fees + subscriptions |
| Educational platform | $30K-100K MRR | Subscription + credential value |
| API / Developer platform | $20K-80K MRR | Usage-based or tiered plans |
| Mobile app (consumer) | $10K-50K MRR | In-app purchases, subscriptions |
| Enterprise SaaS | $100K-500K MRR | High ACV, fewer customers |

Adjust based on:
- Team size and runway
- Market maturity (new category vs. crowded space)
- Geographic focus (global vs. regional)
- Funding stage (bootstrapped vs. funded)

## Revenue Breakdown Structure

### For Subscription Products

| Component | Fields |
|-----------|--------|
| Plan tiers | Name, monthly price, annual price (discount), what's included |
| User distribution | How many users per tier, % of total |
| Conversion rate | Free-to-paid %, tier-to-tier upgrade % |
| Churn rate | Monthly churn per tier |
| ARPU | Average revenue per user (across all tiers) |

### For Marketplace Products

| Component | Fields |
|-----------|--------|
| Transaction fee | % per transaction |
| Average transaction value | Dollar amount |
| Transaction volume | Monthly count |
| Subscription tiers | Optional premium tiers for sellers/buyers |
| Take rate | Effective % of GMV |

### For Usage-Based Products

| Component | Fields |
|-----------|--------|
| Pricing units | What's metered (API calls, storage, users, etc.) |
| Tier thresholds | Free tier limits, paid tier tiers |
| Average usage | Per user per month |
| Overage pricing | Cost per unit beyond tier |

## Conversion Attribution

Map what drives users from free to paid:

1. List all paid features / paywalls in the product
2. Estimate what % of conversions each paywall drives
3. Validate: percentages must sum to ~100%
4. Identify the "primary conversion trigger" — the single most powerful paywall

This attribution directly feeds the Gap Audit — broken paywalls = broken revenue.

## KPI Target Table

Every SRD Success Reality needs this table:

| Metric | How to Estimate |
|--------|----------------|
| **MRR** | User-provided or estimated from project type |
| **Total registered users** | MRR ÷ ARPU ÷ conversion rate |
| **MAU** | 50-70% of registered (depends on product stickiness) |
| **Paying users** | MRR ÷ ARPU |
| **Free-to-paid conversion** | Industry benchmark: 2-5% for consumer, 5-15% for B2B, 5-10% for education |
| **Monthly churn** | Industry benchmark: 3-7% for consumer, 2-5% for B2B SaaS |
| **Avg session duration** | 5-10 min (tool), 15-30 min (learning), 20-45 min (social/community) |
| **Sessions per week (paid)** | 2-5× depending on stickiness |
| **Sessions per week (free)** | 0.5-2× |
| **NPS** | 40+ = strong PMF, 20-40 = good, <20 = needs work |

## Content Volume Estimation

For content-driven products, estimate the content volume at the target date:

- Published content units (courses, articles, listings, etc.)
- User-generated content volume (posts, reviews, submissions)
- Growth rates (content per day/week)

This is important because an SRD with "10 courses" feels different from one with "500 courses" — it affects persona behavior and journey design.

## Revenue Consistency Checks

After generating all SRD sections, validate:

1. **Persona LTV check**: Sum of (persona_LTV × persona_user_count) should approximate MRR × 12
2. **Revenue % check**: All persona revenue percentages sum to ~100%
3. **User % check**: All persona user percentages sum to ~100%
4. **Conversion attribution check**: All journey conversion percentages sum to ~100%
5. **Revenue at risk check**: Sum of journey revenue-at-risk ≤ Target MRR
6. **Paying user check**: Sum of paying users across tiers = total paying user count
7. **MRR reconstruction**: Sum of (tier_price × tier_users) = Target MRR

If any check fails, adjust the numbers until they're internally consistent. Flag the adjustment and explain the reasoning.
