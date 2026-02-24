# Skill Frontmatter Spec

Each `SKILL.md` MUST start with YAML frontmatter.

Required keys:

- `name`
- `version`
- `owner`
- `maturity`
- `description`

Recommended:

- Keep `version` aligned with the skill's `VERSION` file.
- Keep `name` human-readable; use folder name as canonical machine id.
- Use RFC2119 keywords for normative requirements in the skill body.
