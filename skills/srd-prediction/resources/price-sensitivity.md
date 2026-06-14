# Price Sensitivity

## Core Principle

The paper measures purchase intent at the concept's *implied* price and **never varies price.** But for
an offer or an info-product, **price is the decision.** Is dojo-factory's $500 diagnostic priced right?
What does demand look like at $299 vs $699? SDV adds a synthetic price-sensitivity layer the paper lacks.

The cardinal rule from `elicitation-methods.md` still holds: **never ask a synthetic respondent to "set
the price."** Elicit reactions at *specific* prices and aggregate.

## Method 1 — Van Westendorp PSM (where's the acceptable band?)

Ask each respondent four price questions about the concept, in character, as free-text-then-map:

1. **Too cheap** — "At what price would this feel so cheap you'd doubt its quality?"
2. **Cheap / bargain** — "At what price would this be a great deal you'd grab?"
3. **Expensive** — "At what price would this start to feel expensive but you'd still consider it?"
4. **Too expensive** — "At what price would you definitely walk away?"

Aggregate the four cumulative curves across the panel to find:
- **PMC** (Point of Marginal Cheapness) — lower edge of the acceptable range.
- **PME** (Point of Marginal Expensiveness) — upper edge.
- **OPP** (Optimal Price Point) — where "too cheap" and "too expensive" cross; the price minimizing
  resistance.
- **IPP** (Indifference Price Point) — where "cheap" and "expensive" cross; the typical/expected price.
- **Acceptable range** = [PMC, PME] → `willingness_to_pay.acceptable_range` in the forecast.

VW answers "what price band will the market tolerate?"

## Method 2 — Gabor–Granger (what's the revenue-maximizing price?)

Sweep the concept across discrete price points (`concept.price_test.price_points`, e.g.
`[199, 299, 499, 699, 999]`). At **each** price, run the normal purchase-intent elicitation and get a
distribution. Then build the demand curve:

```
est_conversion(price) = f(purchase_intent distribution at that price)
revenue_index(price)   = price × est_conversion(price)
```

- `f(...)` = top-box / top-2-box share of the PI pmf, then the **intent→behavior discount** from
  `calibration.md` (and the real calibrated map at T3).
- The **revenue-maximizing price** is the `argmax revenue_index` → `willingness_to_pay.opp` for GG.
- Report the full `demand_curve` array so the user sees the shape, not just the peak.

GG answers "which price makes the most money?" Run **both** VW and GG when you can: VW gives the
tolerable band, GG finds the optimum inside it.

## Per-Segment Demand Curves `[new scope]`

Different personas have different wallets — a bootstrapping founder and a funded team do not share a
WTP. **Compute the curve per segment**, weighted by `user_pct`/`revenue_pct`, and surface the split:

> "Blended optimum is $499, but P01 (bootstrapper, 15% of users) drops off above $299 while P03
> (funded, 40% of revenue) is price-insensitive to $999 — consider a two-tier offer."

Segment heterogeneity is often the most valuable finding: it turns "what price?" into "which prices, for
whom?" (tiering, regional pricing, payment plans).

## Worked Framing — dojo-factory $500

- Anchor price 500; sweep `[199, 299, 499, 699, 999]`.
- VW → is $500 inside [PMC, PME], or already past PME for the core segment?
- GG → does `revenue_index` peak at 499, or would 699 with lower conversion actually earn more?
- Per-segment → who churns out of the funnel at $500 and how much revenue do they represent?

## Combining With Calibration

`est_conversion` is **modeled** (not measured) at T0–T2 and **must be labeled as such**. At **T3**, the
synthetic→actual map (`calibration.md`) replaces the modeled `f(...)` with a calibrated one, and the
revenue-maximizing price becomes a trustworthy number rather than a directional one.

## Common Mistakes

1. **Asking the model to "pick a price."** That's the DLR sin again — sweep specific prices instead.
2. **One blended curve, hiding segment heterogeneity.** The split is usually the insight.
3. **Treating modeled `est_conversion` as calibrated.** Below T3 it's directional; say so.
4. **Non-monotonic demand curves passed off as signal.** Demand should fall as price rises; if it
   doesn't, it's noise — add samples or flag it.
5. **Running GG without VW (or vice-versa).** The band and the optimum answer different questions.
