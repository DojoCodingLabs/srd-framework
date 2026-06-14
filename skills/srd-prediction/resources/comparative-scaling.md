# Comparative Scaling — The Accuracy Spine

## Core Principle

**Ranking is the reliable signal; absolute scores are not.** Design every run so that the headline
output is a *comparison*, not a number.

This is the cleverest move in SDV, and it's grounded in the paper's own data. Their flagship result —
**ρ ≈ 90% correlation attainment** — is a *ranking* metric (how well synthetic mean intent orders
concepts the way humans do). Their *absolute* distribution match (KS, plus a residual positivity
offset they admit they never fully removed) is the weaker, anchor-dependent part. So the method is
*most* trustworthy when asked **"which of these is better?"** and *least* trustworthy when asked
**"what's the absolute score?"** SDV is built to ask the question the method answers well.

Conveniently, this is also the shape of almost every real decision: *which* creative, *which* headline,
*which* price, *which* offer framing.

## Two Comparative Instruments

### 1. Best–Worst Scaling (MaxDiff-style) — preferred for 3–6 variants

Show each respondent the full variant set. Ask them to pick the **one they'd most likely buy** and the
**one they'd least likely buy**, with a one-line reason for each. From this:

```
best_worst_share(V) = (times V picked best − times V picked worst) / times V appeared
```

Range −1.0 (always worst) to +1.0 (always best). This single number is robust, intuitive, and far more
stable across reruns than absolute means.

For >6 variants, show **random subsets of 4–5** per respondent (a MaxDiff design) and aggregate shares
across subsets — never make a respondent rank 10 things at once.

### 2. Pairwise Duels — preferred for 2 variants, or to break ties

For each pair (A, B), ask "which would you actually buy, A or B — and why?" Aggregate into:

```
win_rate(V) = duels won by V / duels V participated in
```

Optionally fit a **Bradley–Terry** strength per variant from the pairwise outcomes for a smooth ranking
when there are many pairs. Win-rate alone is fine for small sets.

## Aggregation Across the Panel

- Compute best-worst share / win-rate **per persona**, then aggregate **weighted by `user_pct`** for a
  market-share view and **weighted by `revenue_pct`** for a revenue view. Report whichever the decision
  needs (a creative aimed at high-LTV segments should be judged on the revenue-weighted ranking).
- Always surface **segment splits**: a variant can win overall but lose the segment that actually pays.
  "V2 wins the panel but V1 wins P01/P03 (62% of revenue)" is a more useful sentence than a single rank.

## Rank Stability (mandatory)

Run the comparison at least **twice** (resampled). If the top rank holds, mark `rank_stability:
"stable"`. If it flips, mark `"unstable"` and say so loudly in the report — an unstable ranking means
the variants are effectively tied and the decision should be made on other grounds (cost, brand fit) or
with a real human test. Never present an unstable ranking as a winner.

## Single-Variant Runs Still Get a Comparison

If the concept has only one variant (`comparison_mode: single`), **manufacture a reference** so the
respondent still makes a relative judgment rather than an inflated absolute one. Use, in order of
preference:

1. The **status quo / "do nothing"** alternative the segment currently uses.
2. A **known competitor** or the category default.
3. A **neutral baseline** version of the concept (e.g., a plain-vanilla rewording).

Report absolute purchase-intent for context, but frame the verdict as "preferred over status quo by X%
of the segment," which is both more honest and more accurate than a bare 1–5.

## How Comparative and Absolute Combine

- **Comparative rank** = the headline. It decides *which* variant.
- **Absolute purchase-intent distribution** = context. It hints at *how strong* demand is in absolute
  terms — but is only trusted as a go/no-go once calibrated to real outcomes (T3, see `calibration.md`).
- **Demand Score** (`construct-battery.md`) blends constructs into a 0–100; when two variants' Demand
  Scores are within noise, the **comparative signal breaks the tie**, not the decimal places.

## Common Mistakes

1. **Averaging absolute scores and calling it a ranking.** Use best-worst/duels; they're more stable.
2. **Ignoring segment weights.** An unweighted panel average can crown a variant the paying segment hates.
3. **Too many variants at once.** >6 → use MaxDiff subsets, not a 10-way rank.
4. **Reporting a winner from an unstable ranking.** If rank flips on resample, it's a tie — say so.
5. **Letting a single-variant run produce a lonely inflated 4.2/5** instead of a comparison vs. status quo.
