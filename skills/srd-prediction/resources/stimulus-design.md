# Stimulus Design

## Core Principle

**Garbage stimulus → garbage forecast.** The respondent reacts to whatever you show them, so the
stimulus must match *how the buyer actually encounters the product* — price visible, CTA visible, in
context. Abstract descriptions ("a course about React") inflate scores because they hide the friction a
real buyer feels; show the thing as it's really met.

## Per-Surface Stimulus

### offer
Show the **full offer as the buyer sees it**: the promise/outcome, what's included (deliverables), the
**price (prominent)**, the timeframe, any guarantee, and any proof (testimonials, logos, results).
Missing price or proof changes the reaction entirely — include what's really on the page. Enables the
price test (`price-sensitivity.md`).

### creative
The creative **is** an image, so feed the **actual asset** (`asset_ref`) through Claude's vision — don't
paraphrase it. The paper used concept slides and noted text-only "mildly reduces performance"; for ad
creative the visual carries most of the signal. Add only minimal context (placement: feed/story/banner).
Frame the reaction as a **scroll-stopping moment**: "this just appeared in your feed — do you stop, and
do you get it in 2 seconds?" Up-weight `appeal` + `comprehension` (`construct-battery.md`).

### copy
Show the **headline + section copy in context**, as a landing block, not as a bare sentence. Test
variants of wording with everything else held constant (see Variant Hygiene). Up-weight `comprehension`
+ `believability`.

### feature
Present the feature as a user **first meets it** — an in-app announcement, a changelog line, a
pricing-page bullet — not an internal spec. Ask whether it would change their behavior or willingness to
pay/stay. Up-weight `differentiation`.

## Multimodal Handling

- **FLR (default):** feed the real image/screenshot directly to the vision-capable model for the
  reaction step. The buyer sees pixels; so should the synthetic buyer.
- **SSR (embeddings):** the embedding model is text-only, so **transcribe the asset to a faithful text
  description first** (as the paper did with GPT-4o on concept images), then embed the *reaction*. Still
  generate the reaction from the actual image — only the anchor-mapping step uses text.
- Keep a record of any transcription in the concept file so runs are reproducible.

## Variant Hygiene

- **Isolating a driver** (which headline? which price?) → vary **exactly one element** across variants,
  hold everything else constant. Otherwise you can't attribute the win.
- **Choosing overall** (which whole concept?) → vary the whole concept, but keep the *format* of
  presentation identical so you're comparing concepts, not production polish.
- Give variants neutral internal labels (V1/V2). **Never** leak which one you favor into the stimulus.

## Priming Separation (don't leak evaluation cues)

Keep the two prompts clean and separate:
- **Persona priming** = *who the respondent is* (demographics, JTBD, wallet, skeptic stance) — from
  `elicitation-methods.md`.
- **Stimulus** = *what they see* — the offer/creative/copy/feature, exactly as encountered.

Never write evaluation cues into the stimulus ("rate this amazing offer," "our best-selling course").
Loaded framing manufactures positivity and destroys the signal. The stimulus should be as neutral as the
real-world encounter — and no more flattering.

## Common Mistakes

1. **Abstract descriptions instead of the real encounter.** Show price, CTA, proof — the actual page.
2. **Paraphrasing an ad instead of feeding the image.** For creative, feed the asset to vision.
3. **Changing several things between variants** so the winner is unattributable.
4. **Leaking the favored variant** or writing flattering framing into the stimulus.
5. **Forgetting placement/context** (feed vs. landing vs. pricing page) — context shapes the reaction.
