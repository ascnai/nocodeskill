# Changelog

## 0.0.4 - 2026-02-26

- Added canonical plugin control flow coverage for `control.plugins.get` and `control.plugins.validate_definition`.
- Updated contracts/scenarios to use canonical tool names with explicit parameter overlays (no pseudo tool IDs).
- Aligned workflow validation guidance with strict payload shape (`{ "workflow": { ... } }`).
- Updated skill/docs references for deterministic plugin-definition readback and preflight validation.

## 0.0.3 - 2026-02-25

- Added plugin publication guidance for UI authoring with `params_ui` best practices.
- Added explicit use of `control.registry.resolve_options` for `options_source` driven fields.
- Added `include_definition=true` pattern for reading plugin-definition examples via `control.plugins.list`.

## 0.0.2 - 2026-02-24

- Fixed skill delegation path to `skills/ascn-integrations/SKILL.md`.
- Normalized `SKILL.md` frontmatter name to stable skill id (`ascn-operator`).
- Bumped version metadata in `SKILL.md`, `VERSION`, and repository indexes.

## 0.0.1 - 2026-02-21

- Initial bootstrap release.
