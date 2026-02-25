# Gap Audit Methodology

## Core Principle

The Gap Audit is the **decision engine** of SRD. It cross-references personas against journeys to produce a prioritized fix list ordered by revenue impact. It answers: "What should we build next, and why?"

## Four Layers

### Layer 1: Persona × Journey Impact Matrix

Build a matrix where:
- **Rows** = Personas (P01, P02, ...)
- **Columns** = Journeys (J1, J2, ...)
- **Cells** = Impact markers:
  - **X** = Essential (persona needs this journey to use the product)
  - **$** = Conversion trigger (this journey is what makes the persona pay)
  - *(empty)* = Not relevant to this persona

Example:
```
         J1   J2   J3   J4   J5   J6
P01      X    $    X    X    -    -
P02      X    $    X    X    -    X
P03      X    $    -    $    X    -
```

**What this reveals**: If J4 is broken and P01, P02, P03 all need it, that's 3 personas blocked. If J4 is a conversion trigger ($) for P03, that's direct revenue loss.

### Layer 2: Revenue at Risk per Journey

For each journey, calculate the revenue at risk:

```
Revenue at risk = (Journey's conversion attribution %) × Target MRR × (1 - Journey Score / 100)
```

Example:
- J4 drives 18% of conversions
- Target MRR = $100K
- J4 score = 25%
- Revenue at risk = 0.18 × $100,000 × 0.75 = **$13,500/month**

Sort journeys by revenue at risk descending. This is the priority order.

### Layer 3: Persona Viability Summary

Segment personas into viability tiers based on their complete lifecycle:

| Tier | Criteria | Meaning |
|------|----------|---------|
| **Viable** (>60% of their journeys work) | Can complete core lifecycle, will convert | Low risk |
| **Partial** (40-60% of their journeys work) | Will convert but churn faster | Medium risk |
| **Broken** (<40% of their journeys work) | Cannot complete core journey, high churn | High risk — revenue at risk |

For each persona, calculate viability:
1. List their primary journeys
2. Average the journey scores (weighted by whether it's a conversion trigger)
3. Classify into tier

### Layer 4: Prioritized Fix List

Group fixes into three tiers:

#### T0: Revenue Blockers
- Fixes that directly unblock payment flows or conversion points
- Journey score impact: moves a Revenue-Critical journey from <50% to >75%
- Revenue at risk: highest
- **These ship first, no exceptions**

#### T1: Value Delivery
- Fixes that complete user journeys but don't directly gate payment
- Journey score impact: moves Value-Delivery journeys from <50% to >75%
- Revenue at risk: medium (retention, not conversion)

#### T2: Retention & Growth
- Fixes that improve retention, reduce churn, or enable growth loops
- Journey score impact: moves journeys from 75% to 90%+
- Revenue at risk: lowest immediate impact, highest long-term impact

### Fix Entry Format

Each fix in the list should include:

| Field | Description |
|-------|-------------|
| **ID** | T0-1, T0-2, T1-1, T1-2, T2-1... |
| **Description** | What needs to be built/fixed |
| **Journey** | Which journey(s) this fix affects |
| **Personas** | Which personas are unblocked by this fix |
| **Revenue at risk** | Dollar amount per month |
| **Effort** | S (hours), M (days), L (weeks) |
| **Dependencies** | What must be done first |

### Quick Wins Section

After the tiered list, identify quick wins:
- Existing code in PRs/branches that just needs merging
- Configuration changes (enable a feature flag, uncomment code)
- Copy/content changes (update a CTA, fix a broken link)

Quick wins are "free revenue" — they cost almost nothing to ship.

### Implementation Sequence

Finally, produce an ordered implementation sequence that respects:
1. Dependencies (can't fix J4 if J1 is broken)
2. Revenue impact (T0 before T1 before T2)
3. Effort efficiency (quick wins interspersed with larger work)
4. Persona viability (unblock "Broken" tier personas first)

## Scoring Consistency Rules

- A journey's score must be consistent with its acceptance criteria (if 3/5 criteria fail, score can't be 80%)
- Revenue at risk must be internally consistent (all journey revenue-at-risk values should sum to ≤ Target MRR)
- Fix effort estimates should be calibrated to the project's tech stack (a "small" fix in a Rails app ≠ a "small" fix in a microservice architecture)

## Common Mistakes to Avoid

1. **Revenue math errors**: Revenue at risk values that don't add up or exceed the target MRR
2. **Missing dependencies**: Listing T1 fixes that depend on T0 fixes without noting the dependency
3. **Ignoring existing work**: Not checking for existing PRs, branches, or half-built features before estimating effort
4. **Equal weighting**: Treating all journeys as equally important when some drive 30% of conversions and others drive 2%
5. **Effort without anchoring**: Saying "Large effort" without specifying what "Large" means for this project (days? weeks? months?)
