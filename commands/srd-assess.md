---
description: "Run a full SRD assessment through guided dialogue — the deepest, most thorough mode. Works on codebases, PRDs, or just ideas."
---

# SRD Assess: Guided Dialogue — $ARGUMENTS

You are running the Synthetic Reality Development (SRD) assessment in **guided dialogue mode**. This is the deepest and most thorough mode. You will walk the user through a structured conversation to build a complete SRD framework for their project.

**Input**: Codebase in the working directory, a PRD document (provided as $ARGUMENTS), or just an idea description.
**Output**: Complete SRD framework written to `srd/` directory (6 files).

Load the `srd-analysis` skill for methodology knowledge before proceeding.

---

## Phase 1: Project Understanding (5-8 questions)

Ask questions ONE AT A TIME. Wait for each answer before asking the next. Adapt follow-ups based on responses. Maximum 10 questions before moving to generation.

### Core Questions

**Q1 — Product Identity**
"What does your product do in one sentence? Who is it for?"

**Q2 — Revenue Model**
"How does (or will) this product make money? Options: subscriptions, transactions/marketplace fees, usage-based pricing, enterprise contracts, advertising, or something else?"

**Q3 — Revenue Target**
"What revenue target should we plan backwards from? (e.g., $50K MRR, $1M ARR, 10K users). If you're unsure, I can estimate based on your product type."

**Q4 — Current State**
"Is there an existing codebase I should explore, a PRD document I should read, or are we working from an idea?"

**Q5 — User Landscape**
"Describe the 2-3 most important types of users. Who pays? Who creates value? Who brings others in?"

**Q6 — Core Value Loop**
"What is the one thing a user MUST successfully do for your product to survive? (e.g., complete a purchase, publish content, hire someone, finish a course)"

**Q7 — Known Pain Points** *(skip if idea-stage)*
"What are the biggest gaps or broken experiences right now? Where do users get stuck or drop off?"

**Q8 — Competitive Context**
"What alternatives exist? Why would someone choose your product over them?"

### Adaptive Follow-Ups

Based on answers, you may ask 1-2 targeted follow-ups:
- If revenue model is unclear: "Walk me through what happens when a free user becomes a paying user."
- If multi-sided marketplace: "Who are the two sides? What does each side need from the other?"
- If enterprise: "Who is the buyer? Who is the end user? Are they the same person?"

### Shortcut

If the user says "just generate it" or similar, skip remaining questions and work with what you have. Acknowledge what you'll assume and proceed.

---

## Phase 2: Context Gathering (Automatic — runs in parallel with Q&A)

### If a codebase exists in the working directory:

Launch the `codebase-auditor` agent as a subagent to explore:

```
Task(
  subagent_type="codebase-auditor",
  prompt="Explore this codebase and return a structured report. Follow the exploration protocol in your system prompt.",
  run_in_background=true
)
```

### If a PRD document is referenced:

Read the document and extract: stated features, user types, metrics, monetization model, timelines.

### If idea-only:

No automated gathering. All context comes from the questions.

---

## Phase 3: Section-by-Section Generation with Approval Gates

Generate each SRD section, present it to the user, and wait for approval before proceeding. This ensures accuracy and incorporates domain expertise.

### Section 1: Success Reality

Generate the Success Reality based on revenue model and target. Include:
- Platform KPIs table (MRR, MAU, paying users, conversion rate, churn, NPS)
- Revenue breakdown by plan/tier
- Content/feature volume at target
- Conversion attribution (what makes free users pay)

Present to user, then ask:
**"Does this success snapshot feel right? Any numbers to adjust or assumptions to challenge?"**

Wait for approval. Revise if needed.

### Section 2: Synthetic Personas

Generate all personas based on user landscape and project context. Scale count to complexity:
- Simple tool: 3-5 personas
- Standard SaaS: 6-8 personas
- Complex platform: 8-12 personas
- Marketplace: 10-15 personas

Each persona MUST include all fields from the persona schema (see `schemas/persona.schema.yml`).

Present personas to user, then ask:
**"Do these personas ring true? Anyone missing? Anyone feel wrong or redundant?"**

Wait for approval. Add, remove, or revise personas as needed.

### Section 3: Critical Journeys

Generate journey maps based on personas and codebase analysis. One journey per critical user flow.

Each journey MUST include all fields from the journey schema (see `schemas/journey.schema.yml`).

If analyzing a codebase: use real routes and file paths.
If analyzing a PRD: use planned routes.
If idea-only: use projected routes marked `[TBD]`.

Present journeys to user, then ask:
**"Do these journeys cover the critical paths? Any missing flows? Any scored too high or too low?"**

Wait for approval.

### Section 4: Gap Audit Matrix

Generate the gap audit by cross-referencing personas against journeys:
1. Persona × Journey impact matrix
2. Revenue at risk per broken journey
3. Persona viability summary (Viable / Partial / Broken tiers)
4. Tiered fix list (T0 / T1 / T2) with effort estimates
5. Quick wins (existing PRs, config changes, easy fixes)
6. Implementation sequence

Present to user, then ask:
**"Does this prioritization make sense? Anything ranked wrong? Any fixes missing?"**

Wait for approval.

### Section 5: Claude Directive

Generate the machine-readable directive:
1. North Star test (one question that determines shipping readiness)
2. Priority rules (7-10 rules, ordered, tailored to this project)
3. Anti-patterns (5-8 patterns, specific to observed codebase/team tendencies)
4. Persona quick reference (abbreviated YAML)
5. Journey acceptance criteria (binary pass/fail per journey)
6. Current top priorities (top 3-5 from gap audit)

Present to user, then ask:
**"Are these the right rules for your team? Any anti-patterns to add or remove? Does the North Star test capture what 'done' means?"**

Wait for approval.

---

## Phase 4: File Generation

After all five sections are approved:

1. Write `srd/success-reality.md`
2. Write `srd/personas.yml` (YAML format)
3. Write `srd/journeys.md`
4. Write `srd/gap-audit.md`
5. Write `srd/claude-directive.yml` (YAML format)
6. Write `srd/SRD.md` (combined single document with all sections)

Present a summary:
```
SRD Framework generated successfully.

Target: [revenue target]
Personas: [N] ([list names])
Journeys: [N] ([list names with scores])
Top Priority: [T0-1 description] — $[amount]/mo at risk

Files written:
  srd/success-reality.md
  srd/personas.yml
  srd/journeys.md
  srd/gap-audit.md
  srd/claude-directive.yml
  srd/SRD.md
```

---

## Phase 5: Integration Offer

After files are written, offer integration:

**"Would you like me to integrate the SRD into your development workflow? This will:**
1. **Add the Claude Directive to your CLAUDE.md** — priority rules and anti-patterns will guide all future Claude sessions
2. **Create an srd-guardian agent** in `.claude/agents/` — validates work against SRD priorities
3. **Add a PostToolUse hook** — reminds Claude to check SRD alignment after file changes

**Say 'yes' to integrate, 'skip' to keep just the documents, or ask about any specific integration."**

### If user says yes:

**CLAUDE.md Integration:**
- Read existing CLAUDE.md (if it exists)
- Append an "## SRD Framework — Active Directive" section with:
  - Priority rules summary
  - Active anti-patterns summary
  - Current top priority with revenue at risk
  - Links to full SRD files

**Guardian Agent:**
- Create `.claude/agents/srd-guardian/` directory
- Write `agent.json` with sonnet model and read-only tools
- Write `system-prompt.md` with enforcement protocol

**Hook:**
- If `.claude/hooks/hooks.json` exists, merge the SRD PostToolUse hook
- If not, create hooks directory and file

---

## Execution Rules

- NEVER skip the question phase — guided dialogue is what makes assess mode valuable
- NEVER generate all sections at once — pause after each for feedback
- ALWAYS validate revenue math consistency before presenting each section
- If a codebase exists, reference real file paths and routes in journeys
- If the user provides domain expertise that contradicts your analysis, defer to the user
- Keep persona lifecycles specific and story-driven, not generic
