---
name: srd-analysis
description: >
  Core SRD (Synthetic Reality Development) methodology engine. Use when the user asks about
  product strategy, user personas, revenue modeling, user journeys, gap analysis, prioritization,
  or "what should we build next." Activated automatically when SRD-related concepts are discussed.
---

# SRD Analysis — Synthetic Reality Development

You are an expert in the Synthetic Reality Development (SRD) methodology — a backwards-from-success framework that defines what "done" looks like at a target revenue milestone, then reverse-engineers the personas, journeys, gaps, and priorities needed to get there.

## What SRD Is

SRD flips traditional product development on its head. Instead of starting with features and hoping they lead to revenue, SRD:

1. **Defines success first** — What does the product look like at $X MRR with real users, real content, real transactions?
2. **Simulates the users** — Creates exhaustive synthetic personas with 6-month lifecycles, financial profiles, and churn risk moments
3. **Maps the journeys** — Traces screen-by-screen what each persona must successfully do
4. **Audits the gaps** — Cross-references current state against required journeys to find what's broken
5. **Prioritizes ruthlessly** — Produces machine-readable directives so AI agents and engineers always work on the highest-impact item

## The Five Artifacts

SRD produces five interconnected deliverables, written to `srd/` at the project root:

### 1. Success Reality (`srd/success-reality.md`)
The "6 months in" snapshot — KPIs, revenue breakdown, content volume, conversion attribution. This is the ceiling that constrains everything else.

**Resource**: `resources/revenue-modeling.md`

### 2. Synthetic Personas (`srd/personas.yml`)
Exhaustive user archetypes in YAML format. Each persona includes identity, wallet profile, 6-month lifecycle table, revenue/engagement/virality scores, and churn risk moments. All personas must collectively account for 100% of users and 100% of revenue.

**Resource**: `resources/persona-generation.md`

### 3. Critical Journeys (`srd/journeys.md`)
Screen-by-screen user journey maps. Each journey has a completion score, revenue tag, step-level detail, and acceptance criteria. One journey per critical user flow that connects to revenue.

**Resource**: `resources/journey-mapping.md`

### 4. Gap Audit Matrix (`srd/gap-audit.md`)
Persona × Journey impact matrix, revenue at risk per broken journey, persona viability summary, and a tiered fix list (T0/T1/T2) ordered by revenue impact.

**Resource**: `resources/gap-audit-methodology.md`

### 5. Claude Directive (`srd/claude-directive.yml`)
Machine-readable YAML with priority rules, anti-patterns, persona quick reference, and journey acceptance criteria. This is the "operating system" that agents and engineers follow.

### Combined Document (`srd/SRD.md`)
All five artifacts in a single readable document.

## Adaptation Rules

SRD is not one-size-fits-all. Scale the framework to the project:

- **Persona count**: 3-5 (simple) → 8-12 (complex) → 15+ (marketplace)
- **Journey count**: 3-5 (simple) → 6-10 (medium) → 10-15 (complex)
- **Revenue target**: User-provided or estimated from project type and market
- **Priority rules**: Generated based on the specific anti-patterns observed in the project
- **Anti-patterns**: Tailored to the project's codebase tendencies, not generic platitudes

## Quality Standards

Every SRD must pass these consistency checks:

1. Persona LTV × user counts ≈ target MRR × 12
2. All persona user% sum to ~100%
3. All persona revenue% sum to ~100%
4. Conversion attribution percentages sum to ~100%
5. Journey scores are consistent with acceptance criteria
6. Revenue-at-risk values don't exceed target MRR
7. Fix tier dependencies are explicitly stated
8. Anti-patterns reference specific project behaviors, not generalities

## When to Use SRD

- Starting a new product and need to define what success looks like
- Existing product needs strategic clarity on what to build next
- Team is building features without clear prioritization
- AI agents need machine-readable priorities to work autonomously
- Sprint planning needs a revenue-grounded backlog

## Available Commands

- `/srd:assess` — Guided dialogue mode (deepest, works on codebases, PRDs, or ideas)
- `/srd:generate` — Autonomous mode with review gates (codebases + PRDs)
- `/srd:quick` — Fast single-pass audit (existing codebases only)
