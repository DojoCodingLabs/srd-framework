# Construct Battery & Demand Score

## Core Principle

Product success is **multivariate**. The paper measured one construct (purchase intent) plus a single
"relevance" cameo. But a concept can fail for very different reasons — unclear, unbelievable, not
differentiated, mispriced — and a lone purchase-intent number hides *which*. SDV measures a **battery**
of constructs, each with its own anchors, then blends them into one composite **Demand Score** while
keeping the per-construct detail that tells you *why*. More correlated signals also **reduce variance**,
so the composite is more stable than any single read.

## The Eight Constructs

| Construct | The question (in character) | Scale | Reading |
|-----------|------------------------------|-------|---------|
| **purchase_intent** | "Would you actually buy this?" | 1–5 | The core demand signal |
| **appeal** | "How much does this grab you?" | 1–5 | Gut attraction, pre-rational |
| **comprehension** | "Do you understand what this is and what you get?" | 1–5 | Low ⇒ confused copy, not weak demand |
| **differentiation** | "Is this meaningfully different from your alternatives?" | 1–5 | Low ⇒ commodity / me-too |
| **believability** | "Do you believe it'll deliver what it promises?" | 1–5 | Low ⇒ trust/proof gap |
| **price_fairness** | "Is this worth the price shown?" | 1–5 | Value perception at the displayed price |
| **willingness_to_pay** | (elicited via price methods) | currency | The price they'd accept — see `price-sensitivity.md` |
| **share_intent** | "Would you tell someone about this?" | 1–5 | Virality / advocacy proxy |

Measure a **surface-appropriate subset** (you don't need price_fairness for a pure feature concept).
Default subsets:
- **offer** → full battery (including WTP via a price test)
- **creative** → appeal, comprehension, differentiation, purchase_intent, share_intent
- **copy** → comprehension, believability, appeal, purchase_intent
- **feature** → appeal, differentiation, comprehension, purchase_intent

## Composite Demand Score (0–100)

Blend the Likert constructs (each mean normalized from 1–5 to 0–1 as `(mean−1)/4`) with default weights:

| Construct | Default weight |
|-----------|----------------|
| purchase_intent | 0.35 |
| price_fairness | 0.15 |
| appeal | 0.15 |
| comprehension | 0.10 |
| differentiation | 0.10 |
| believability | 0.10 |
| share_intent | 0.05 |

```
demand_score = 100 × Σ( weight_c × (mean_c − 1) / 4 )   over measured constructs,
               with weights renormalized to sum to 1 over whatever subset was measured.
```

`willingness_to_pay` is a **price, not a 0–1 score** — it does not enter the blend directly; its effect
flows in through `price_fairness` and through the price-sensitivity output.

### Surface-specific reweighting

The defaults suit offers. Reweight for the decision at hand:
- **Ad creative** → up-weight `appeal` + `comprehension` (a scroll-stopping, instantly-legible creative
  matters more than price perception, which the landing page handles).
- **Landing copy** → up-weight `comprehension` + `believability` (copy's job is clarity and trust).
- **Feature** → up-weight `differentiation` (a feature earns its keep by being non-commodity).

Always record the weights actually used in the forecast so `demand_score` is reproducible.

## Objection & Driver Mining → the Gap-Audit Bridge `[new scope]`

The paper treated the free-text rationales as a "byproduct." For SDV they are a **primary output**,
because a low score is a dead end but a structured *objection* is an actionable, revenue-tagged fix.

From the elicited reactions (you already have them — don't re-poll), extract **structured** items:

**Objections** (reasons not to buy):
```
{ segment, objection, severity (high|medium|low), frequency (0–1 of segment) }
```
**Drivers** (reasons they want it):
```
{ segment, driver, strength (high|medium|low) }
```

Rules:
- Cluster near-duplicate reactions into one objection with a frequency, don't list raw quotes.
- `severity` reflects how much it blocks the sale; `frequency` how widespread it is in the segment.
- Keep one representative verbatim per objection for color, but the structured fields drive decisions.

### Promotion into `srd/gap-audit.md`

High-severity, high-frequency objections become **demand-tagged fix items** in the gap audit, using a
`D`-prefixed tier that sits alongside the existing T0/T1/T2 code-fix tiers:

| Objection profile | Gap-audit tier | Meaning |
|-------------------|----------------|---------|
| severity high + frequency ≥ 0.4 in a paying segment | **D0** (Demand Blocker) | Kills conversion now — fix the offer/copy/proof/price |
| severity medium, or lower frequency | **D1** (Demand Friction) | Dampens conversion |
| severity low | **D2** (Demand Polish) | Minor lift |

Each promoted item carries the segment, the revenue at risk (segment `revenue_pct` × target MRR), and a
suggested remedy (e.g., "add a guarantee," "show proof," "re-price to $X per `price-sensitivity.md`").
This is how a *prediction* becomes *prioritized work* in the existing SRD operating system.

## Common Mistakes

1. **Reporting only purchase_intent.** You lose the *why*; you can't tell a pricing problem from a
   clarity problem.
2. **Fixed weights regardless of surface.** Re-weight for creative vs. copy vs. offer.
3. **Dumping raw quotes as "objections."** Cluster into structured items with severity + frequency.
4. **Letting WTP into the 0–100 blend directly.** It's a price; route it through price_fairness + the curve.
5. **Forgetting to promote D0 objections into the gap audit.** The bridge back to SRD is the whole point.
