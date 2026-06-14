# Changelog

## [1.1.0] - 2026-06-13

### Added
- **Synthetic Demand Validation (SDV) layer** — the demand-side complement to SRD's supply-side analysis
- `/srd:predict` command — forecast whether an offer, info-product, ad creative, landing copy, feature, or price will sell by polling SRD personas as a synthetic consumer panel
- `srd-prediction` skill with seven methodology resources (elicitation methods, comparative scaling, anchor sets, construct battery, price sensitivity, calibration, stimulus design)
- `demand-forecaster` agent (Opus) — runs the full forecast pipeline end-to-end
- `concept` and `forecast` output schemas; forecasts written to `srd/forecasts/`
- Methodology based on Maier et al. 2025 (SSR purchase-intent elicitation), extended with: comparative-first ranking, capability-aware fidelity tiers (T0 cold-start → T3 Stripe/PostHog outcome-calibrated), optional embeddings-backed true SSR, a multi-construct Demand Score, Van Westendorp + Gabor-Granger price sensitivity, and structured objection mining that feeds `srd/gap-audit.md` as demand-tagged D-tier fixes
- Optional `scripts/ssr_embed.py` helper for embeddings-backed SSR (auto-used only when an embeddings provider is detected; otherwise prompt-only FLR)

### Changed
- `srd-analysis` skill, persona/gap-audit/revenue resources, and the persona schema now reference personas as a pollable panel and accept calibrated demand numbers and demand-tagged fixes (all additive, non-breaking)

## [1.0.0] - 2026-02-25

### Added
- Initial release of SRD Framework plugin
- Three interaction modes: `/srd:assess` (guided), `/srd:generate` (autonomous), `/srd:quick` (fast audit)
- Core SRD analysis skill with methodology resources
- SRD Guardian skill for priority enforcement
- Three specialized agents: srd-analyst (Opus), codebase-auditor (Sonnet), srd-guardian (Sonnet)
- Output schemas for personas, journeys, and directives
- Post-generation integration (CLAUDE.md injection, guardian agent, hooks)
- PostToolUse hook for SRD alignment reminders
