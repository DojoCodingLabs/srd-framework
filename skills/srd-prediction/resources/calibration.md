# Calibration, Capability Detection & Honest Uncertainty

## Core Principle

**Never claim more certainty than the evidence supports.** SDV probes each project, declares a
**fidelity tier**, and sizes its confidence intervals — and its claims — to match. With nothing
detected it runs the paper's zero-shot method and says so plainly ("directional"). As real signal
appears, it tightens and eventually earns the right to make an absolute go/no-go call.

This is the answer to "capability-agnostic": the plugin adapts to whatever the project exposes.

## Step 1 — Capability Detection

Reuse the `codebase-auditor` agent's report (it already finds Stripe + dependencies) and add a few
probes. Detection is **read-only** — never write to or call external services.

| Capability | How to detect | Unlocks |
|------------|---------------|---------|
| **SRD personas** | `srd/personas.yml` exists | T1 — reuse rich personas as the panel |
| **Customer language** | testimonials/reviews/support/FAQ/changelog text, PostHog session notes | T2 — data-derived anchors, real objections |
| **Analytics** | PostHog/GA/Amplitude SDK + events in code | T2 — behavioral proxies, funnel context |
| **Conversion data** | Stripe integration + accessible order/subscription data | T3 — synthetic→actual calibration map |
| **Embeddings** | OpenAI/other embeddings key in env/config | +SSR mode — true SSR instead of FLR |
| **Real assets** | image/screenshot files referenced by the concept | multimodal stimulus |

Record what was found in `calibration_metadata.detected_capabilities`. The tier is the **highest** whose
trigger is satisfied (with +SSR orthogonal — it can ride on any tier).

## Step 2 — Fidelity Tiers

| Tier | Trigger | Behavior | CI width | Allowed claim |
|------|---------|----------|----------|---------------|
| **T0 Cold-start** | nothing detected | Zero-shot FLR; ensembled generic→domain anchors | **Widest** | **Ranking only**; absolute = "directional" |
| **T1 Persona-grounded** | `srd/personas.yml` | Reuse personas; segment-weighted aggregation | Wide | Ranking + relative; absolute still soft |
| **T2 Data-anchored** | customer text / analytics | Anchors & objections from real language; behavioral proxies | Medium | Ranking + better-grounded absolute |
| **T3 Outcome-calibrated** | Stripe + PostHog conversions | Fit synthetic→actual map; learned intent→behavior discount | **Tight** | **Absolute go/no-go enabled** |

The tier is the forecast's honesty header. A T0 forecast that presents a confident absolute verdict is
**invalid** — it must lead with the ranking and caveat the absolutes.

## Step 3 — Outcome Calibration (T3) `[accuracy↑]`

This is the loop the paper structurally could not run (offline dataset). When the project has shipped
variants *and* real conversion data:

1. **Assemble training pairs.** For each historically-shipped concept/variant/price, pair its synthetic
   signal (mean purchase intent or top-2-box share, recomputed the same way) with its **actual observed
   conversion** (from Stripe orders / PostHog funnel).
2. **Fit a monotonic map** `g: synthetic_signal → actual_conversion` (isotonic regression, or Platt
   scaling for a smooth logistic). Monotonic because more synthetic intent should never predict *less*
   real conversion.
3. **Apply `g`** to new forecasts' synthetic signal to produce **calibrated** `est_conversion` (and a
   calibrated revenue-maximizing price in `price-sensitivity.md`).
4. **Tighten CIs** using the residual spread of `g` on held-out pairs.

With too few pairs to fit `g` reliably, stay at T2: report the synthetic ranking, apply the *default*
discount below, and say calibration is pending more data. Never fabricate a map from 2 points.

## Step 4 — Intent → Behavior Discount `[accuracy↑]`

Even perfectly-matched *stated* intent over-predicts *action* (the intention–behavior gap). Convert
intent to estimated action with a discount multiplier:

- **Uncalibrated (T0–T2):** apply a conservative default, e.g. `est_conversion ≈ top_2_box_share × 0.5`
  (a Juster-scale-inspired haircut). This is a *modeled estimate*; label it.
- **Calibrated (T3):** the discount is **learned**, folded directly into `g` above — no separate
  multiplier needed.

Record the effective multiplier in `calibration_metadata.intent_behavior_discount` (1.0 only when T3
data justifies it).

## Step 5 — Familiarity & Confidence Intervals

Estimate a **familiarity_score (0–1)**: how much reliable prior the model plausibly has for *this*
buyer and category. The paper's own boundary — "usefulness is bounded by the knowledge domains in
training data." High for mainstream consumer/dev tooling; low for a novel niche or anything post-cutoff.

Size confidence intervals from three inputs (wider on any of them):

```
CI_width  ∝  tier_factor (T0 widest → T3 tightest)
          ×  (1 − familiarity_score)         # unfamiliar domain ⇒ wider
          ÷  sqrt(effective_samples)         # more samples ⇒ tighter
```

When `familiarity_score` is low, **widen the bands, label the forecast "directional," and recommend a
real human test before a large bet.** Honesty here is a feature: it tells the user when *not* to trust
the synthetic panel.

## What Each Tier May Conclude

- **T0/T1:** "V2 is preferred over V1 (stable across resamples). Absolute demand is directional only."
- **T2:** "V2 wins; objections cluster on price/proof; absolute demand grounded in real buyer language."
- **T3:** "V2 wins; calibrated est. conversion 4.1% at $499 (90% CI 3.4–4.8%); revenue-max price $499.
  Ship V2."

## Common Mistakes

1. **Claiming absolute go/no-go below T3.** Lead with ranking; caveat absolutes.
2. **Fitting a calibration map from a handful of points.** Stay at T2; say calibration is pending.
3. **`intent_behavior_discount = 1.0` without T3 data.** Intent over-predicts action; discount it.
4. **Ignoring familiarity.** A confident forecast in an unfamiliar/post-cutoff domain is a trap.
5. **Calling external services during detection.** Detection is read-only; never write or transact.
