---
description: "Run a fast SRD audit on an existing codebase. Single-pass, no approval gates. Generates a complete SRD framework in under 12 minutes."
---

# SRD Quick: Fast Audit — $ARGUMENTS

You are running the Synthetic Reality Development (SRD) assessment in **quick audit mode**. This is the fastest mode — single-pass, no questions, no approval gates. Explore the codebase, generate all five sections, write all files, present a summary.

**Input**: Existing codebase in working directory (required).
**Output**: Complete SRD framework written to `srd/` directory (6 files).

Load the `srd-analysis` skill for methodology knowledge before proceeding.

---

## Step 1: Validate Codebase Exists

Check for project markers: `package.json`, `Cargo.toml`, `go.mod`, `pyproject.toml`, `Gemfile`, `pom.xml`, or similar.

If no codebase detected:
> "No codebase detected in the current directory. `/srd:quick` requires an existing codebase to audit. Use `/srd:assess` for idea-stage projects or `/srd:generate` for PRD-based projects."

Stop.

If `srd/` already exists:
> "An existing SRD was found. Generating a fresh audit — the previous `srd/` will be overwritten."

---

## Step 2: Rapid Codebase Scan

Do NOT launch a subagent — scan directly for speed. Time budget: 2-3 minutes maximum.

### Scan checklist:

1. **Project identity**: Read `README.md`, `CLAUDE.md`, or package manifest for product description
2. **Routes/Pages**: Scan route definitions, page directories, navigation components
3. **Authentication**: Check for auth providers, role definitions, permission gates
4. **Monetization**: Search for payment/subscription code — Stripe, payment forms, plan gating
5. **Database**: Check migrations, models, type definitions, schemas
6. **Features**: Scan feature directories and assess completeness (full implementation vs. stubs)
7. **Existing plans**: Check for PRDs, specs, planning docs, roadmaps
8. **Dependencies**: Read package manifest for key integrations

### What to extract:

- Product type and market
- Revenue model (or best guess from code)
- User roles/types
- Major feature areas and their completeness
- Tech stack
- Known issues (TODOs, FIXMEs, commented-out code)

---

## Step 3: Generate All Sections (Single Pass)

Generate all five SRD sections in sequence without pausing for approval. Use sensible defaults:

### Success Reality
- Revenue target: Estimate from product type if not stated in docs
- Standard KPI table scaled to product type
- Revenue breakdown based on discovered monetization model
- Content volume based on current codebase state × 6-month projection

### Synthetic Personas
- Generate 5-8 personas (medium complexity default)
- Base personas on discovered user roles and authentication patterns
- Include wallet profiles based on discovered pricing/plans
- Generate 6-month lifecycles referencing actual routes

### Critical Journeys
- Generate 6-10 journeys (one per major user flow discovered)
- Reference real routes from the scan
- Score based on observed feature completeness:
  - Route exists + full implementation = 80-100%
  - Route exists + partial implementation = 50-75%
  - Route exists + stub/empty = 25-50%
  - Route missing = 0-25%

### Gap Audit
- Build persona × journey matrix
- Calculate revenue at risk per journey
- Produce tiered fix list
- Identify quick wins (existing PRs, TODOs, commented-out code)

### Claude Directive
- Generate 5-7 priority rules based on observed patterns
- Generate 4-6 anti-patterns based on codebase tendencies
- Create persona quick reference
- Write journey acceptance criteria
- Set North Star test based on the most critical persona's end-to-end journey

---

## Step 4: Write All Files

Write all six files to `srd/`:

1. `srd/success-reality.md`
2. `srd/personas.yml`
3. `srd/journeys.md`
4. `srd/gap-audit.md`
5. `srd/claude-directive.yml`
6. `srd/SRD.md` (combined)

---

## Step 5: Present Summary

```
## SRD Quick Audit Complete

**Project**: [name from package manifest]
**Target**: [estimated revenue target]
**Personas**: [N] generated
  - [P01 Name] ([Archetype]) — [LTV], [user%]% of users
  - [P02 Name] ([Archetype]) — [LTV], [user%]% of users
  - ...

**Journeys**: [N] mapped
  - [J1 Name]: [score]% [status emoji]
  - [J2 Name]: [score]% [status emoji]
  - ...

**Top Priority**: [T0-1] — [description] ($[amount]/mo at risk)

**North Star**: [The question]. Current answer: [No/Partial].

**Files written:**
  srd/success-reality.md
  srd/personas.yml
  srd/journeys.md
  srd/gap-audit.md
  srd/claude-directive.yml
  srd/SRD.md

Want to integrate SRD into your development workflow? Say 'integrate'.
Want a deeper assessment? Run /srd:assess.
```

---

## Step 6: Integration (if requested)

If the user says "integrate", "yes", or similar:
- Inject directive into CLAUDE.md
- Create srd-guardian agent
- Add PostToolUse hook

---

## Execution Rules

- SPEED is the priority — this mode should complete in 8-12 minutes total
- Do NOT ask questions — use best-guess defaults
- Do NOT pause for approval — generate everything in one pass
- DO reference real routes and file paths from the codebase
- DO validate revenue math internally (consistency checks) but don't present the validation
- If something is ambiguous, make a reasonable assumption and note it in the output
- Flag areas of low confidence in the summary (e.g., "Revenue model unclear — assumed subscription")
