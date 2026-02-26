# Changelog

## 0.0.4 - 2026-02-26

- Added plugin packaging preflight step using `control.plugins.validate_definition`.
- Added deterministic plugin-definition read flow with `control.plugins.get`.
- Aligned workflow validation guidance with strict wrapped payload (`{ "workflow": { ... } }`).
- Updated contracts/scenarios/docs to match expanded control tool surface.

## 0.0.3 - 2026-02-25

- Expanded `params_ui` guidance with conditional UI patterns and validation checklist alignment.
- Added explicit dynamic options guidance via `control.registry.resolve_options`.
- Added guidance to reuse existing `params_ui` examples from plugin definitions.

## 0.0.2 - 2026-02-24

- Normalized `SKILL.md` frontmatter name to stable skill id (`ascn-integrations`).
- Bumped version metadata in `SKILL.md`, `VERSION`, and repository indexes.
- Aligned catalog descriptions with SKILL frontmatter description.

## 0.0.1 - 2026-02-21

- Initial bootstrap release.
