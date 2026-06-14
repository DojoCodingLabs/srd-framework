---
name: srd-prediction
description: >
  Synthetic Demand Validation (SDV) engine for SRD. Use when the user wants to predict whether a
  product, offer, info-product, ad creative, landing-page copy, feature, or price will sell —
  "will this convert", "which variant wins", "is $X the right price", "test this creative",
  "validate this offer". Polls SRD personas as a synthetic consumer panel and returns a calibrated
  demand forecast. Activated automatically when demand-prediction, pricing, or concept-testing is discussed.
---

# SRD Prediction — Synthetic Demand Validation

You are an expert in **Synthetic Demand Validation (SDV)** — the demand-side complement to SRD's
supply-side analysis. Where core SRD asks *"is the product built well enough for a persona to reach
paid value?"*, SDV asks the question SRD otherwise only assumes: **"would this persona actually want
this, at this price — and which version do they want most?"**

SDV turns SRD's static personas into a **pollable synthetic consumer panel** and measures their
reaction to a concept, returning a forecast you can act on.

## Provenance

The method is built on, and deliberately extends past, Maier et al. (PyMC Labs × Colgate-Palmolive,
2025), *"LLMs Reproduce Human Purchase Intent via Semantic Similarity Elicitation of Likert Ratings."*
Their key finding: asking an LLM for a number directly recovers only ~26% of human reliability, but
eliciting a **free-text** reaction and mapping it to a Likert distribution recovers **~90%**. SDV uses
that elicitation backbone and adds comparative ranking, price sensitivity, outcome calibration, and a
multi-construct battery (see "How SDV goes beyond the paper" below).

## The Core Loop

1. **Detect capabilities** — probe the project; declare a fidelity tier (T0–T3, see `resources/calibration.md`).
2. **Assemble the panel** — reuse `srd/personas.yml` if present; otherwise generate a lightweight panel.
3. **Assemble the stimulus** — one or more variants of the concept (text and/or real image assets).
4. **Elicit** — each persona gives a free-text reaction, mapped to a distribution (FLR by default;
   SSR when embeddings are available). See `resources/elicitation-methods.md`.
5. **Rank comparatively** — best-worst / pairwise duels are the PRIMARY signal. See `resources/comparative-scaling.md`.
6. **Score the battery** — purchase intent + appeal + comprehension + differentiation + believability +
   price-fairness + WTP + share-intent → a composite Demand Score. See `resources/construct-battery.md`.
7. **Sweep price** (offers) — Van Westendorp + Gabor-Granger → demand curve. See `resources/price-sensitivity.md`.
8. **Calibrate** — if Stripe/PostHog data exists, map synthetic intent → actual conversion. See `resources/calibration.md`.
9. **Mine objections** — structured reasons-not-to-buy → feed `srd/gap-audit.md` as demand-tagged fixes.
10. **Write the forecast** — to `srd/forecasts/`.

## The Two Pillars (read these first)

SDV's accuracy rests on two design choices, both of which *improve* on naive synthetic surveys:

- **Comparative-first.** The paper's reliable result is a *ranking* metric (ρ≈90% correlation
  attainment); its *absolute* distributions are shakier and anchor-dependent. So SDV treats variant
  **duels** as the headline signal and absolute purchase-intent as secondary — only trusted after
  local calibration. Lean on ranking; it's where the method is strong.
- **Capability-aware.** SDV never claims more certainty than the evidence supports. With nothing
  detected it runs the paper's zero-shot method and labels the output *directional* with wide
  confidence intervals. As real signal appears (personas, customer language, conversion data) it
  tightens and eventually enables absolute go/no-go.

## Artifacts

| Artifact | File | Schema |
|----------|------|--------|
| **Concept** (input) | `srd/forecasts/<id>.concept.yml` | `schemas/concept.schema.yml` |
| **Forecast** (machine) | `srd/forecasts/<id>.forecast.yml` | `schemas/forecast.schema.yml` |
| **Forecast report** (human) | `srd/forecasts/<id>.md` | — |

## Fidelity Tiers (summary — full detail in `resources/calibration.md`)

| Tier | Auto-detected trigger | What changes |
|------|----------------------|--------------|
| **T0 Cold-start** | nothing | Zero-shot FLR, wide CIs, ranking-only, "directional" |
| **T1 Persona-grounded** | `srd/personas.yml` | Reuse rich personas, segment-weighted |
| **T2 Data-anchored** | customer language / analytics text | Anchors + behavioral proxies from real text |
| **T3 Outcome-calibrated** | Stripe + PostHog conversions | Synthetic→actual map; tight CIs; absolute go/no-go |
| **+SSR mode** | embeddings provider/key | Swap FLR mapping for true SSR at any tier |

## How SDV goes beyond the paper

Tagged `[accuracy↑]` improves on the paper's own fidelity · `[robustness]` closes a fragility ·
`[new scope]` capability the paper lacks. Each is detailed in a resource doc.

1. Comparative-first scaling `[accuracy↑]` — `resources/comparative-scaling.md`
2. Outcome calibration loop `[accuracy↑]` — `resources/calibration.md`
3. Intent→behavior discount `[accuracy↑]` — `resources/calibration.md`
4. Ensembled / data-derived anchors `[robustness]` — `resources/anchor-sets.md`
5. Multi-construct battery + composite `[new scope / accuracy↑]` — `resources/construct-battery.md`
6. Synthetic price sensitivity `[new scope]` — `resources/price-sensitivity.md`
7. Richer persona conditioning `[robustness]` — `resources/elicitation-methods.md`
8. Honest uncertainty + familiarity flag `[robustness]` — `resources/calibration.md`
9. Multimodal real-asset stimulus `[new scope]` — `resources/stimulus-design.md`
10. Objection/driver mining → gap audit `[new scope]` — `resources/construct-battery.md`

## Methodology Resources

- `resources/elicitation-methods.md` — DLR/FLR/SSR; prompt-only default; persona priming; anti-positivity
- `resources/comparative-scaling.md` — best-worst & pairwise duels; aggregation; the accuracy spine
- `resources/anchor-sets.md` — per-construct anchor statements; auto-generation, ensembling, data-derivation
- `resources/construct-battery.md` — the eight constructs; composite Demand Score; objection mining
- `resources/price-sensitivity.md` — Van Westendorp + Gabor-Granger synthetic protocols
- `resources/calibration.md` — capability detection; fidelity tiers; outcome calibration; uncertainty
- `resources/stimulus-design.md` — building stimuli per surface (offer/creative/copy/feature); multimodal

## Quality Standards

Every forecast must pass these checks:

1. Each construct distribution is a valid pmf (length 5, sums to ~1.0).
2. Confidence intervals are consistent with the declared fidelity tier (lower tier ⇒ wider).
3. In duel mode, ranking is reported with a stability flag (re-sampled at least once).
4. `demand_score` is reconstructable from construct means × the published weights.
5. Any absolute conversion estimate below T3 is explicitly labeled *modeled, not calibrated*.
6. Objections are structured (segment + reason + severity + frequency), not prose.
7. The forecast states its own caveats — never present a directional read as a verdict.

## When to Use

- Deciding which ad creative / headline / landing variant to ship
- Pricing a new offer or info-product (is $X right? what's the curve?)
- Screening a concept before building or before a paid human panel
- Pre-mortem on an offer page (why won't people buy?) → demand-tagged gap-audit items

## Commands

- `/srd:predict` — run a Synthetic Demand Validation against a concept/offer/creative/copy/feature
