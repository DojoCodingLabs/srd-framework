---
description: "Run an autonomous SRD assessment with review gates. Explores the codebase or PRD automatically, generates each section, and pauses for approval."
---

# SRD Generate: Autonomous Mode — $ARGUMENTS

You are running the Synthetic Reality Development (SRD) assessment in **autonomous generation mode**. You will explore the project context independently, generate each SRD section, and pause at review gates for human approval.

**Input**: Codebase in working directory + optional PRD (provided as $ARGUMENTS or referenced in project docs).
**Output**: Complete SRD framework written to `srd/` directory (6 files).

Load the `srd-analysis` skill for methodology knowledge before proceeding.

---

## Phase 1: Autonomous Context Gathering

### Step 1: Detect Available Context

Check what input sources exist:

1. **Codebase**: Look for project markers — `package.json`, `Cargo.toml`, `go.mod`, `pyproject.toml`, `Gemfile`, `pom.xml`, or similar.
2. **PRD/Docs**: Check for planning documents — `$ARGUMENTS`, `docs/`, `README.md`, `CLAUDE.md`, `*.prd`, `specs/`.
3. **Existing SRD**: Check if `srd/` already exists (offer to regenerate or update).

If NEITHER codebase NOR PRD exists:
> "This mode requires a codebase or PRD to analyze. For idea-stage projects, use `/srd:assess` which guides you through questions to build the context."

Stop.

### Step 2: Launch Codebase Auditor

If a codebase exists, launch the `codebase-auditor` agent:

```
Task(
  subagent_type="codebase-auditor",
  prompt="Explore this codebase and return a structured report of: routes/pages, database schema, authentication config, monetization/payment code, feature completeness, and tech stack. Follow the exploration protocol in your system prompt.",
  run_in_background=true
)
```

### Step 3: Analyze Documentation

While the auditor runs, read available documentation:

1. README.md — Product description, setup instructions, stated goals
2. CLAUDE.md — Architecture decisions, conventions, tech stack details
3. PRD documents — Features, user types, metrics, monetization, timeline
4. Planning docs — Any existing strategy, roadmap, or audit documents
5. Package manifest — Dependencies reveal product type and integrations

### Step 4: Synthesize Context

Combine codebase audit + documentation analysis into a unified understanding:

- **Product type**: What is this? (SaaS, marketplace, tool, platform, etc.)
- **Revenue model**: How does/will it make money?
- **User types**: Who uses it? How many distinct types?
- **Feature state**: What works? What's partially built? What's missing?
- **Tech stack**: What frameworks, databases, services?
- **Market context**: Who are the competitors? What's the differentiation?

Determine appropriate scale:
- Persona count (based on user types discovered)
- Journey count (based on routes/flows discovered)
- Revenue target (from docs or estimated from product type)

Present the synthesis to the user:
> "Here's what I found. Based on [product type], I'll generate [N] personas, [N] journeys, targeting [revenue target]. Does this framing look right, or should I adjust anything before generating?"

Wait for confirmation.

---

## Phase 2: Generation with Review Gates

### Gate 1: Success Reality

Generate the Success Reality section:
- KPIs at target revenue
- Revenue breakdown by tier/model
- Content/feature volume at target
- Conversion attribution

Present. Ask: **"Approve this success snapshot, or suggest changes?"**
Wait for "approve" or feedback. Revise if needed.

### Gate 2: Synthetic Personas

Generate all personas per the methodology in `resources/persona-generation.md`.
Validate consistency: user% sums to ~100%, revenue% sums to ~100%, LTVs are realistic.

Present. Ask: **"Approve these personas, or suggest changes?"**
Wait for approval.

### Gate 3: Critical Journeys

Generate journey maps referencing real routes from the codebase audit.
Score each journey based on observed feature completeness.

Present. Ask: **"Approve these journeys and scores, or suggest changes?"**
Wait for approval.

### Gate 4: Gap Audit Matrix

Cross-reference personas × journeys. Calculate revenue at risk.
Produce tiered fix list (T0/T1/T2). Identify quick wins.

Present. Ask: **"Approve this prioritization, or suggest changes?"**
Wait for approval.

### Gate 5: Claude Directive

Generate the machine-readable directive with project-specific rules.

Present. Ask: **"Approve this directive, or suggest changes?"**
Wait for approval.

---

## Phase 3: Write Files

After all gates are approved:

1. Write `srd/success-reality.md`
2. Write `srd/personas.yml`
3. Write `srd/journeys.md`
4. Write `srd/gap-audit.md`
5. Write `srd/claude-directive.yml`
6. Write `srd/SRD.md` (combined)

Present summary with file list, top priority, and persona/journey counts.

---

## Phase 4: Integration Offer

Same integration flow as `/srd:assess`:
- CLAUDE.md injection
- Guardian agent creation
- PostToolUse hook installation

---

## Execution Rules

- Launch the codebase auditor EARLY — it runs in background while you analyze docs
- At each gate, present the full section content — don't summarize
- If the user says "approve all" or "looks good, continue" at any gate, proceed to the next
- If the user provides corrections, incorporate them before moving on
- Reference real file paths and routes from the codebase audit in journeys
- Validate revenue math consistency before each gate
