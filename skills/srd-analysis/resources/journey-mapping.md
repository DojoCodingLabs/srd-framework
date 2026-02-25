# Journey Mapping Methodology

## Core Principle

A Critical Journey is a **complete user story from intent to outcome** that connects to revenue. It's not a feature list — it's a screen-by-screen walkthrough of what a persona must successfully do for the product to survive.

## What Makes a Journey "Critical"

A journey is critical if any of these are true:
1. **Revenue-gating**: The journey contains a payment step or paywall
2. **Activation**: The journey is how new users discover value (first-time experience)
3. **Retention**: The journey is what brings users back repeatedly
4. **Viral**: The journey generates referrals or social proof
5. **Ecosystem**: The journey creates value that other journeys depend on

If a user flow doesn't match any of these criteria, it's a feature — not a critical journey.

## Scaling Rules

| Project Type | Journey Count | Guidance |
|-------------|---------------|----------|
| Single-purpose tool | 3-5 | Signup → Core Action → Payment → Retention loop |
| SaaS product | 6-10 | One per major user flow that connects to revenue |
| Marketplace | 8-12 | Buyer journeys + seller journeys + platform journeys |
| Platform (multi-pillar) | 10-15 | One per pillar's primary flow + cross-cutting journeys |

## Journey Structure

### Header
- **ID**: Sequential (J1, J2, ...) — used for cross-referencing
- **Name**: Descriptive action chain (e.g., "Discovery → Signup → Onboarding")
- **Personas**: Which personas use this journey (array of persona IDs)
- **Revenue tag**: "Revenue-Critical" or "Value-Delivery"
- **Current score**: 0-100% completion score (see scoring below)

### Step-by-Step Map

Each journey is broken into numbered steps with 4 columns:

| Column | Description | Example |
|--------|-------------|---------|
| **Step** | Sequential number | 1, 2, 3... |
| **User action** | What the persona physically does | "Clicks 'Apply' button on bounty card" |
| **Screen/Route** | The actual page or URL | `/projects/bounties/[id]` or `[TBD]` |
| **What must happen** | System behavior required | "Application form opens, pre-fills user profile data" |
| **Data required** | What data needs to exist | "User profile complete, bounty still accepting applications" |

**Step guidelines**:
- Each step should be one discrete user action (not a paragraph of behavior)
- Screen/Route should reference real routes when analyzing a codebase, or `[TBD]` for ideas/PRDs
- "What must happen" describes the system's required response — not what it currently does
- Data required identifies dependencies that could block the step

### Journey Score Assessment

Score each journey 0-100% based on how much of the happy path works today:

| Score Range | Meaning |
|-------------|---------|
| 0-25% | **Broken** — Core steps don't work. Persona cannot complete the journey. |
| 25-50% | **Scaffolded** — Structure exists but critical steps are missing or broken. |
| 50-75% | **Partial** — Most steps work but gaps block completion or degrade experience. |
| 75-90% | **Mostly complete** — Works end-to-end with minor issues. |
| 90-100% | **Complete** — Full happy path works, edge cases handled. |

**Scoring rules when analyzing a codebase**:
- Check if routes/pages exist for each step
- Check if data models support the required fields
- Check if API endpoints exist and function
- Check for dead ends (buttons that don't work, empty states with no CTAs)
- Check for broken auth/permission gates

**Scoring rules for PRDs/ideas**:
- Score based on how well-specified each step is (vague = lower score)
- Give 0% to steps that aren't mentioned at all
- Give 50% to steps that are described but have no technical spec

### Acceptance Criteria

After the step map, each journey needs binary pass/fail acceptance criteria:

```
- [ ] PASS/FAIL: [Specific testable statement]
- [ ] PASS/FAIL: [Specific testable statement]
```

Each criterion must be:
- **Binary**: Either it works or it doesn't. No "partially works."
- **Testable**: A person (or automated test) can verify it in under 60 seconds.
- **Specific**: References the exact screen, button, or behavior.

If a criterion fails, link it to the fix tier: `(fix: T0-1)` or `(fix: T1-3)`

### Revenue Impact

Calculate the revenue impact of each journey:
- What % of conversions does this journey drive?
- How many personas depend on it?
- What's the dollar amount at risk if this journey is broken?

Formula: `Revenue at risk = (% of conversions this journey drives) × Target MRR`

## Journey Dependency Graph

After mapping all journeys, document the dependency chain:
- Which journey is the root? (usually Signup/Onboarding)
- Which journeys gate other journeys? (usually the payment/conversion journey)
- Which journeys are independent? (can work even if others are broken)
- Which journeys cross-cut? (affect multiple other journeys, like gamification)

This dependency graph determines implementation order — you can't fix J4 if J1 and J2 are broken.

## Common Mistakes to Avoid

1. **Feature lists instead of journeys**: "User can upload a file" is a feature. "User creates a project → uploads assets → invites collaborator → publishes" is a journey.
2. **Missing the payment step**: Every journey that touches revenue must include the exact moment money changes hands (or a paywall appears).
3. **Optimistic scoring**: Scoring a journey at 80% when the payment step is broken. One broken critical step drops the effective score to 0% for revenue purposes.
4. **Ignoring dependencies**: Scoring J4 independently when J4 depends on J2 working. If J2 is broken, J4's effective score is 0%.
5. **Too granular**: 30-step journeys are unmanageable. Keep to 5-12 steps per journey. If you need more, you have two journeys.
