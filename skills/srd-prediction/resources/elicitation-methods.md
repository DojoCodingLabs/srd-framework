# Elicitation Methods

## Core Principle

**Never ask a synthetic respondent for a number.** Asking an LLM "rate your purchase intent 1–5"
collapses to a safe "3/4" and recovers only ~26% of human reliability (the paper's DLR result).
Instead: elicit a **brief free-text reaction in character**, then map that text to a distribution over
the Likert scale in a *separate* step. This is the single most important rule in SDV.

## The Three Methods (and which to use)

| Method | What it is | Reliability (paper) | Needs embeddings? | When SDV uses it |
|--------|-----------|---------------------|-------------------|------------------|
| **DLR** Direct Likert Rating | Ask for the integer directly | KS 0.26, dist. useless | No | **Never** as a primary signal (baseline only) |
| **FLR** Follow-up Likert Rating | Free-text → LLM-as-Likert-expert maps it | KS 0.72, ρ≈85% | No | **Default** (prompt-only, zero setup) |
| **SSR** Semantic Similarity Rating | Free-text → embed → cosine to anchors → pmf | KS 0.88, ρ≈90% | Yes | **Optional** (auto-enabled if embeddings detected) |

SDV defaults to **FLR** because it needs no external infrastructure and still vastly outperforms DLR.
When an embeddings provider is detected, SDV upgrades to **SSR** (`scripts/ssr_embed.py`) for the
extra fidelity. Both produce the same artifact: a **per-respondent probability mass function (pmf)**
over the 5 Likert points, per construct.

## FLR Protocol (default, prompt-only)

Run this per respondent, per construct, per variant.

### Step 1 — Prime the persona (richer than the paper)

The paper conditioned on demographics only — and gender/region/ethnicity didn't replicate. SDV
conditions on everything SRD already manufactures, because that's what actually drives info/B2B
purchase. Build a system prompt from the persona:

- **Demographics**: age, location, income (the two axes — age & income — the paper found *do* replicate).
- **Psychographics / JTBD**: goals, pain points, the job they're hiring a product to do.
- **Wallet reality**: monthly_spend, budget pressure, what $X actually means to them.
- **Stance**: "You are skeptical by default. You have alternatives, including doing nothing. You guard
  your money. You are not trying to be agreeable."

This skeptic + budget framing is the primary defense against positivity bias (see below).

### Step 2 — Elicit the free-text reaction

Show the stimulus (text and/or image). Ask the construct's question in-character. Request a **brief
(1–3 sentence) honest reaction**, not a rating. Example for purchase_intent:

> "You've just seen this. Reacting honestly as yourself — would you actually buy it? Say what you're
> thinking in a sentence or two."

A good reaction sounds like a real person: *"$500 is steep for a coach I've never heard of. If there
were a guarantee or proof it worked, maybe — but right now I'd keep scrolling."*

### Step 3 — Map text → pmf (the "Likert-rating expert" pass)

In a **separate** call/turn, act as a rating expert and map the reaction to a pmf over 1–5 using the
construct's anchor set (`anchor-sets.md`). **You MUST give the expert examples of what each rating's
language looks like** — the paper found that without examples the distribution comes out unrealistically
narrow. Output a distribution, not a point: e.g. `[0.05, 0.35, 0.45, 0.12, 0.03]`, not "2".

The mapping is soft on purpose: ambiguous reactions ("I'd probably try it if it's cheap") legitimately
spread probability across adjacent points. Preserving that spread is what reproduces realistic human
distributions.

## SSR Protocol (optional, when embeddings detected)

Replace Step 3 with embedding similarity:

1. Embed the free-text reaction and each of the 5 anchor statements (and ensemble sets).
2. Response likelihood of point *r* ∝ cosine(reaction, anchor_r) − min_r cosine (the ε-floor subtraction
   the paper uses to control variance).
3. Normalize to a pmf; optionally apply temperature `T` to control "smearing" (paper: ε=0, T=1 as the
   rule-of-thumb default).
4. Average pmfs across the anchor ensemble.

This is the *only* part of SDV that touches external infra. If the embeddings call fails or no provider
is configured, **fall back to FLR cleanly** — never error out.

## Sampling & Variance Control

- Take **≥3 samples per respondent per construct** (the paper used n=2; more is better for stable pmfs).
- Use a non-trivial temperature (~0.7–1.0) so samples actually vary; average the resulting pmfs.
- Aggregate respondent pmfs into a segment pmf, **weighted by persona `user_pct`** (and `revenue_pct`
  for a revenue-weighted view). Report both the mean and a confidence interval (`calibration.md`).

## Defeating Positivity Bias

Synthetic consumers over-rate everything unless actively constrained. Stack these:

1. **Skeptic stance** in the system prompt (above).
2. **Budget salience** — make the price concrete against their wallet ("$500 is ~X% of your monthly income").
3. **The do-nothing option** — always remind them that *not buying* and *keeping the status quo* are valid.
4. **Moment-of-truth framing** for behavioral proxies — "card in hand, on the page right now."
5. **Comparative mode** (`comparative-scaling.md`) — forcing a choice between variants naturally
   suppresses uniform 5/5 inflation, because they must discriminate.

## Common Mistakes

1. **Asking for the number** (DLR). The cardinal sin. Always free-text first.
2. **Skipping the examples** in the mapping step → narrow, unrealistic distributions.
3. **Collapsing to a point estimate** too early. Keep the pmf until the very end.
4. **Unprimed personas** → everything rates 4–5. Prime skeptic + budget every time.
5. **n=1 sampling** → noisy, non-reproducible pmfs. Use ≥3.
6. **Erroring when embeddings are absent** instead of falling back to FLR.
