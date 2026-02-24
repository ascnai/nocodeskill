# Repository Layout Spec

## Required Top-Level Directories

- `skills/`
- `template/`
- `spec/`
- `.claude-plugin/`

## Skill Directory Contract

Each skill under `skills/<skill-id>/` MUST contain:

- `SKILL.md`
- `README.md`
- `VERSION`
- `CHANGELOG.md`
- `contracts/`
- `references/`
- `agents/`

## Naming

- `skill-id` MUST be lowercase kebab-case.
- Keep one skill per folder.

## Versioning

- Every skill has an independent semantic version (`VERSION`).
- Contract or behavior changes MUST be reflected in that skill's changelog.
