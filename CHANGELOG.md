# Changelog

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
