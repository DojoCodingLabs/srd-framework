# SRD Framework — Synthetic Reality Development

A backwards-from-success product methodology for AI-native development teams. Define what "done" looks like, then reverse-engineer the path to get there.

**Built by [DojoCodingLabs](https://github.com/DojoCodingLabs). Works on any project.**

---

## What is SRD?

Most products fail not because the code is bad, but because the team is building the wrong things in the wrong order.

SRD flips traditional product development on its head. Instead of starting with features and hoping they lead to revenue, you:

1. **Define success first** — What does the product look like at $X MRR with real users and real transactions?
2. **Simulate the users** — Create exhaustive synthetic personas with 6-month lifecycles, financial profiles, and churn risk moments
3. **Map the journeys** — Trace screen-by-screen what each persona must successfully do
4. **Audit the gaps** — Cross-reference current state against required journeys to find what's broken
5. **Prioritize ruthlessly** — Produce machine-readable directives so AI agents and engineers always work on the highest-impact item

The result: a machine-readable operating system for your development team that answers "what should we build next?" with dollar amounts attached.

---

## Quick Start

### Install

In Claude Code, add the marketplace and install:

```bash
# Step 1: Add the SRD Framework marketplace
/plugin marketplace add DojoCodingLabs/srd-framework

# Step 2: Install the plugin
/plugin install srd-framework@srd-framework
```

For local development:

```bash
git clone https://github.com/DojoCodingLabs/srd-framework.git
cd srd-framework
/plugin marketplace add .
/plugin install srd-framework
```

### Update

```bash
claude plugin marketplace update DojoCodingLabs/srd-framework
claude plugin update srd-framework@srd-framework
```

### Modes

| Mode | Command | Best For | Time |
|------|---------|----------|------|
| **Guided Dialogue** | `/srd:assess` | Ideas, PRDs, any project | 20-30 min |
| **Autonomous** | `/srd:generate` | Codebases + PRDs | 15-20 min |
| **Fast Audit** | `/srd:quick` | Existing codebases | 8-12 min |
| **Demand Validation** | `/srd:predict` | Will it sell? Which variant? What price? | 10-15 min |

### Try It

```bash
# On an existing codebase — fast audit
cd your-project
/srd:quick

# Starting from an idea
/srd:assess "A marketplace for freelance developers"

# With a PRD document
/srd:generate docs/product-requirements.md
```

---

## What Gets Generated

Every mode produces 6 files in an `srd/` directory at your project root:

### 1. Success Reality (`srd/success-reality.md`)

The "6 months in" snapshot — KPIs, revenue breakdown, content volume, and conversion attribution at your target revenue.

```markdown
| Metric              | Target    | Reasoning                        |
|---------------------|-----------|----------------------------------|
| MRR                 | $50,000   | North star                       |
| Paying users        | 1,000     | 500 Pro + 200 Premium + 300 Free |
| Free-to-paid        | 8%        | Industry avg for B2B SaaS        |
| Monthly churn       | 5%        | Offset by new conversions        |
```

### 2. Synthetic Personas (`srd/personas.yml`)

Exhaustive user archetypes in machine-readable YAML. Each persona includes identity, wallet profile, 6-month lifecycle, revenue/engagement/virality scores, and churn risk moments.

```yaml
- id: "P01"
  name: "Sarah"
  archetype: "Solo Founder (US)"
  identity:
    age: 29
    location: "Austin, TX"
    background: "Former PM at a SaaS startup. Building her own product."
    goals: "Ship MVP, find first 10 customers, reach $5K MRR"
  wallet_profile:
    income: "$0/month (bootstrapping from savings)"
    plan_progression: "Free (Day 1) -> Pro (Week 2) -> Annual (Month 3)"
    upgrade_trigger: "Hits team collaboration limit on free plan"
    ltv: 588
    user_pct: 15
    revenue_pct: 20
```

### 3. Critical Journeys (`srd/journeys.md`)

Screen-by-screen user journey maps with completion scores. Each step references real routes from your codebase (or `[TBD]` for ideas).

```markdown
### J2: Free -> Pro Conversion (Score: 65%)

| Step | User Action               | Route          | What Must Happen               |
|------|--------------------------|----------------|-------------------------------|
| 1    | Hits a Pro-only feature  | /dashboard     | Upgrade modal appears          |
| 2    | Clicks "Upgrade to Pro"  | /pricing       | Plan comparison loads          |
| 3    | Selects Pro plan         | /checkout      | Stripe checkout initializes    |
| 4    | Completes payment        | /checkout      | Subscription activated         |
| 5    | Redirected to dashboard  | /dashboard     | Pro features unlocked          |
```

### 4. Gap Audit Matrix (`srd/gap-audit.md`)

Persona x Journey impact matrix, revenue at risk per broken journey, and a tiered fix list.

```markdown
### T0: Revenue Blockers ($38,000/mo at risk)

| ID   | Fix                          | Journey | Revenue  | Effort |
|------|------------------------------|---------|----------|--------|
| T0-1 | Complete checkout flow       | J2      | $18,000  | M      |
| T0-2 | Fix OAuth redirect loop      | J1      | $12,000  | S      |
| T0-3 | Enable email verification    | J1      | $8,000   | S      |
```

### 5. Claude Directive (`srd/claude-directive.yml`)

Machine-readable priority rules, anti-patterns, and acceptance criteria. This is the "operating system" that AI agents follow.

```yaml
priority_rules:
  - id: 1
    rule: REVENUE_GATE_FIRST
    description: "Revenue-Critical tasks beat all others"
    example: "Fix checkout before adding dark mode"

anti_patterns:
  - id: "AP-1"
    name: ARCHITECTURE_ASTRONAUT
    trigger: "Developer says 'let me refactor this first'"
    response: "Is the refactor blocking a journey? No? Move on."

north_star:
  question: "Can a new user go from signup to paid value in under 10 minutes?"
  current_answer: "No"
  target_answer: "Yes"
```

### 6. Combined Document (`srd/SRD.md`)

All five sections in one readable document for humans.

---

## Synthetic Demand Validation (`/srd:predict`)

The five artifacts above validate the **supply side** — "is the product *built* well enough for a
persona to reach paid value?" But every demand number in them (conversion rates, the revenue target) is
a heuristic. `/srd:predict` adds the missing **demand side**: it turns your personas into a **pollable
synthetic consumer panel** and forecasts whether an offer, info-product, ad creative, landing copy,
feature, or price will actually sell.

```bash
# Is the $500 offer priced right? What's the demand curve?
/srd:predict --surface offer --price "7-day diagnostic, $500, [paste offer]"

# Which ad creative wins?
/srd:predict --surface creative --compare ad-v1.png ad-v2.png ad-v3.png

# Which headline converts?
/srd:predict --surface copy --compare "Ship faster" vs "Stop guessing what to build"
```

**How it's built — and how it goes further.** The elicitation backbone comes from Maier et al. (PyMC
Labs × Colgate-Palmolive, 2025), *["LLMs Reproduce Human Purchase Intent via Semantic Similarity
Elicitation of Likert Ratings"](https://arxiv.org/abs/2510.08338)* — which recovers ~90% of human
test–retest reliability by eliciting free-text reactions and mapping them to Likert distributions,
versus ~26% for asking the model for a number directly. SDV extends it with:

- **Comparative-first ranking** — leans on the paper's reliable 90% *ranking* signal (best-worst / duels), not its shakier absolutes.
- **Capability-aware fidelity tiers** — auto-detects what the project exposes and scales its confidence to match: `T0` cold-start (zero-shot, directional) → `T1` persona-grounded → `T2` data-anchored (anchors/objections from real reviews/support) → `T3` outcome-calibrated (maps synthetic intent → **actual Stripe/PostHog conversions**). With an embeddings key it upgrades to true SSR.
- **A construct battery** — purchase intent, appeal, comprehension, differentiation, believability, price-fairness, willingness-to-pay, share-intent → one composite **Demand Score**, plus *why*.
- **Synthetic price sensitivity** — Van Westendorp + Gabor-Granger sweeps → a per-segment demand curve and a revenue-maximizing price.
- **Structured objection mining** — the reasons-not-to-buy flow back into `srd/gap-audit.md` as demand-tagged **D-tier** fixes with revenue at risk attached.

Outputs are written to `srd/forecasts/<id>.{concept.yml, forecast.yml, md}` — machine-readable and
human-readable, matching the rest of SRD. Runs with **zero setup** (prompt-only) and gets sharper as
your project exposes more real data.

---

## Integration

After generating the SRD, the plugin offers to integrate it into your development workflow:

### CLAUDE.md Injection
Appends SRD priority rules and anti-patterns to your project's `CLAUDE.md`. Every future Claude session will follow these priorities automatically.

### SRD Guardian Agent
Creates an `srd-guardian` agent in `.claude/agents/` that validates work against SRD priorities. Ask it "should I work on X?" and it'll tell you if there's higher-impact work available.

### PostToolUse Hook
Adds a lightweight hook that reminds Claude to check SRD alignment after file changes. Not blocking — just a compass nudge.

---

## Philosophy

SRD was born from a simple observation: both AI agents and human engineers get stuck on non-vital work while revenue-critical paths remain broken.

The framework takes inspiration from:
- **Amazon Working Backwards** (PR/FAQ) — define the outcome first
- **Jobs-to-be-Done** — understand what users hire the product to do
- **User Story Mapping** (Jeff Patton) — visualize the complete user journey
- **Outcome-Driven Development** — measure outcomes, not outputs

What SRD adds:
- **Synthetic personas with financial profiles** — every persona traces to revenue
- **Machine-readable directives** — AI agents can parse priorities autonomously
- **Revenue-grounded prioritization** — every fix has a dollar amount attached
- **Anti-patterns** — explicit rules for what NOT to do

Or as the framework's creator put it: *"It's like that Simpsons episode where Homer looks fit but all the fat is hidden behind his back. The app works and looks good from the front — tech debt can wait."*

---

## Plugin Components

| Component | Purpose |
|-----------|---------|
| **Skills** | `srd-analysis` (methodology engine), `srd-prediction` (synthetic demand validation), `srd-guardian` (priority enforcement) |
| **Agents** | `srd-analyst` (Opus, generates SRD), `demand-forecaster` (Opus, runs demand forecasts), `codebase-auditor` (Sonnet, explores code), `srd-guardian` (Sonnet, validates alignment) |
| **Commands** | `/srd:assess`, `/srd:generate`, `/srd:quick`, `/srd:predict` |
| **Hooks** | PostToolUse reminder after Write/Edit operations |
| **Schemas** | Persona, Journey, Directive, Concept, and Forecast YAML schemas |

---

## Contributing

We welcome contributions. See [CONTRIBUTING.md](.github/CONTRIBUTING.md) for guidelines.

Ideas for contribution:
- Templates for specific project types (mobile apps, APIs, marketplaces)
- Additional output formats (JSON, PDF)
- Integration with project management tools (Linear, Jira)
- Localization of persona generation for non-English markets
- Visualization tools for the gap audit matrix

---

## Credits

Created by [DojoCodingLabs](https://github.com/DojoCodingLabs).

SRD methodology developed through building the [Dojo Platform](https://github.com/DojoCodingLabs/dojo-os) — a three-pillar educational platform for developers, founders, and freelancers.

---

## License

[MIT](LICENSE)
