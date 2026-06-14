---
description: "Predict whether a product, offer, info-product, ad creative, landing copy, feature, or price will sell. Polls SRD personas as a synthetic consumer panel and writes a calibrated demand forecast."
---

# SRD Predict: Synthetic Demand Validation — $ARGUMENTS

You are running **Synthetic Demand Validation (SDV)** — the demand-side complement to SRD. Core SRD
asks "is the product built?"; this asks "would anyone want it, at this price, and which version most?"

**Input**: A concept to validate — passed via `$ARGUMENTS` as a description, a path to a `.concept.yml`,
or one or more asset paths — plus whatever the project exposes (personas, Stripe, PostHog, embeddings).
**Output**: A demand forecast written to `srd/forecasts/` (3 files).

Load the `srd-prediction` skill for methodology before proceeding.

---

## Argument Parsing

`$ARGUMENTS` may contain a concept description and optional flags:

- `--surface offer|creative|copy|feature` — the concept type (infer if omitted)
- `--compare` — duel mode: expect ≥2 variants and rank them (the comparative-first default when ≥2 are given)
- `--price` — run the price-sensitivity sweep (offers only)
- a path to an existing `srd/forecasts/<id>.concept.yml` — re-run a saved concept
- one or more image/screenshot paths — multimodal creative/landing stimulus

If `$ARGUMENTS` is empty, ask one question: *"What do you want to validate — an offer, an ad creative,
landing copy, a feature, or a price? Paste the concept (or variants), or point me at the asset(s)."*

---

## Phase 1 — Capability Detection

Per `resources/calibration.md`. Detect (read-only) and declare the fidelity tier:

1. **Personas** — does `srd/personas.yml` exist? (→ T1, reuse as panel)
2. **Customer language** — reviews/testimonials/support/FAQ/changelog text present? (→ T2 anchors/objections)
3. **Analytics** — PostHog/GA/Amplitude SDK + events? (→ T2 behavioral proxies)
4. **Conversion data** — Stripe + accessible orders/subscriptions? (→ T3 outcome calibration)
5. **Embeddings** — an embeddings key in env/config? (→ +SSR mode)
6. **Assets** — real images/screenshots referenced? (→ multimodal stimulus)

For a codebase, you may launch `codebase-auditor` (it already finds Stripe + deps) and read its report
rather than re-scanning. **Never call or write to external services** — detection only.

State the result briefly:
> "Detected: [capabilities]. Running at **[tier]**[ +SSR]. [One line on what that means for trust.]"

---

## Phase 2 — Assemble Panel & Stimulus

- **Panel**: reuse `srd/personas.yml` if present; otherwise generate a small segment-spanning panel and
  note it's generated. (If no SRD exists and the user wants the richest read, offer: *"No personas
  found — want me to run `/srd:quick` first for a grounded panel, or proceed with a generated one?"*)
- **Stimulus**: build variants per `resources/stimulus-design.md` — as the buyer really encounters them
  (price/CTA/proof visible; real assets fed to vision). Enforce variant hygiene.

Write the assembled input to `srd/forecasts/<id>.concept.yml` (conforms to `schemas/concept.schema.yml`)
so the run is reproducible.

---

## Phase 3 — Forecast

Delegate the heavy lifting to the `demand-forecaster` agent:

```
Task(
  subagent_type="demand-forecaster",
  prompt="Run Synthetic Demand Validation for the concept at srd/forecasts/<id>.concept.yml.
          Detected tier: <tier>; capabilities: <list>; ssr_mode: <bool>.
          Follow your pipeline. Write all three artifacts to srd/forecasts/."
)
```

The agent: elicits free-text reactions → maps to distributions (FLR default / SSR if available) →
ranks comparatively (the headline) → scores the construct battery → sweeps price (if `--price`) →
calibrates (intent→behavior discount; synthetic→actual map at T3) → mines structured objections.

For a quick single-variant run you may execute the pipeline inline instead of delegating — but always
follow the same methodology and write the same artifacts.

---

## Phase 4 — Present & Bridge to Gap Audit

Present the human report (`srd/forecasts/<id>.md`) summary:

```
## Demand Forecast: [Concept] — [tier]

**Verdict**: [ranking + recommended action — lead with the reliable comparative signal]

**Ranking**:
  1. [V2] — best-worst +0.41, win 68%, Demand 72/100 [stable]
  2. [V1] — best-worst −0.41, win 32%, Demand 58/100

**Price** (if run): VW band $[PMC]–$[PME] · revenue-max $[OPP] · [the $X question, answered]

**Top demand blockers**:
  - [D0] [objection] — [segment], [freq]% — fix: [remedy]

**Confidence**: [tier-appropriate caveat]. [What a real human test would add.]

Files: srd/forecasts/<id>.concept.yml, .forecast.yml, .md
```

Then offer the bridge back into SRD:
> "Want me to promote the D0/D1 demand blockers into `srd/gap-audit.md` as demand-tagged fixes (with
> revenue at risk per segment)? Say 'promote'."

If the user says **promote** and `srd/gap-audit.md` exists, add the objections as `D0/D1/D2` items in
the existing tiered-fix format (segment, revenue at risk = `revenue_pct` × target MRR, suggested
remedy), consistent with the **Demand-Validation Fixes (D-tier)** section of the `srd-analysis` skill's `resources/gap-audit-methodology.md`.

---

## Execution Rules

- **Comparative-first**: when ≥2 variants exist, the ranking is the headline; absolute scores are secondary.
- **Honesty over confidence**: never present an absolute go/no-go below T3, or an unstable ranking as a
  winner. State the fidelity tier every time.
- **Never ask a respondent for a number** — free-text reaction first, then map (see the skill).
- **Read-only detection** — never write to or transact with Stripe/PostHog/etc.
- **Reproducible** — always write the `.concept.yml` so the run can be re-run or audited.
- If embeddings are absent, run FLR — never error out reaching for SSR.
