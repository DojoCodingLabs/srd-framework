# Persona Generation Methodology

## Core Principle

Synthetic personas are not marketing archetypes. They are **exhaustive simulations** of real humans with specific demographics, financial situations, 6-month behavioral timelines, and measurable churn risk moments. Every persona must be traceable to a revenue contribution.

## Scaling Rules

| Project Complexity | Persona Count | Guidance |
|-------------------|---------------|----------|
| Simple (single-user-type tool) | 3-5 | Focus on power user, casual user, and churner |
| Medium (multi-role app, SaaS) | 6-10 | One per distinct user archetype + 1-2 edge cases |
| Complex (marketplace, multi-sided platform) | 10-15 | One per side of the marketplace + cross-cutting personas |
| Enterprise (B2B + B2C hybrid) | 12-20 | Buyer personas + end-user personas + admin personas |

## Required Fields Per Persona

Every persona MUST include all of these fields. No shortcuts.

### Identity Block
- **ID**: Sequential (P01, P02, ...) — used for cross-referencing
- **Name**: A human first name that implies cultural background
- **Archetype**: Short descriptor (e.g., "Career Switcher (LatAm)", "Power User / Community Leader")
- **Age**: Specific number, not a range
- **Location**: City + Country (not just "US" or "Europe")
- **Background**: 2-3 sentences covering education, current job, relevant experience
- **Goals**: What they want to achieve using this product (specific, not vague)
- **Pain points**: What frustrates them about current alternatives
- **Tech stack**: Tools and technologies they're comfortable with
- **Languages**: Language codes (e.g., ["en", "es"])

### Wallet Profile
- **Income**: Monthly with currency (e.g., "$800/month")
- **Plan progression**: Timeline of plan changes (Free → Pro → Annual)
- **Upgrade trigger**: The specific moment that causes conversion (be exact)
- **Monthly spend**: Dollar amount or range
- **LTV**: Lifetime value in dollars (calculated from plan × retention)
- **User percentage**: What % of total user base this persona represents
- **Revenue percentage**: What % of total revenue this persona generates

**Consistency rule**: All user percentages must sum to ~100%. All revenue percentages must sum to ~100%. LTV × user count must approximate the target revenue when projected.

### 6-Month Lifecycle Table
A time-based table showing what the persona does at each milestone:

| Column | Description |
|--------|-------------|
| Time | Day 1, Day 2-3, Week 1, Week 2, Month 1, Month 2, Month 3, Month 6 |
| Actions | What the persona specifically does (concrete, not abstract) |
| Features touched | Which screens/routes/features they interact with |
| Classification | "Revenue-Critical" (affects conversion/payment) or "Value-Delivery" (builds engagement) |

**Lifecycle guidelines**:
- Day 1 MUST include discovery channel (how they found the product)
- Each entry should be 2-4 sentences of specific behavior
- Upgrade moment must be a specific, story-driven entry
- Include at least one moment where the persona almost churns but doesn't
- Month 6 entry should describe the "success state" — what their life looks like now

### Scores (1-10 scale)
- **Revenue**: How much direct revenue this persona generates (1=free forever, 10=highest LTV)
- **Engagement**: How active they are on the platform (1=monthly visitor, 10=daily power user)
- **Virality**: How many other users they bring (1=tells nobody, 10=active evangelist)

### Churn Risk Moments
Array of 2-4 specific time+condition pairs:
- **Time**: When the churn risk peaks (e.g., "Week 2", "Month 3")
- **Condition**: What specific event triggers potential churn

### Cross-References
- **Primary journeys**: Array of journey IDs this persona relies on (e.g., ["J1", "J2", "J4"])
- **Conversion trigger**: The single most important moment that converts this persona
- **Critical note**: Optional flag for lifecycle-breaking issues (e.g., "J4 currently broken — cannot complete core journey")

## Persona Ecosystem

After generating all personas, map the ecosystem:

1. **Money flow**: Who pays? Who earns? Who attracts others?
2. **Dependency chains**: Which personas depend on others existing? (e.g., freelancers need clients posting bounties)
3. **Revenue anchors**: Which 2-3 personas generate >50% of revenue?
4. **Acquisition multipliers**: Which personas have the highest virality scores?
5. **Vulnerability**: If one persona type churns, who else is affected?

## Common Mistakes to Avoid

1. **Vague personas**: "A developer who wants to learn" — too generic. Name them, age them, locate them, give them a salary.
2. **Revenue disconnect**: Personas that are "important" but don't trace to revenue. Every persona must have an LTV.
3. **Happy-path only**: Forgetting churn risk moments. Real users almost quit multiple times.
4. **Uniform personas**: All personas having the same upgrade trigger or same lifecycle. Diversity in behavior is the point.
5. **Missing ecosystem links**: Treating personas as independent when they depend on each other (e.g., a marketplace needs both buyers and sellers).
