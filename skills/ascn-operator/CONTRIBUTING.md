# Contributing

## Scope

Changes in this repo are contract-sensitive. Treat `SKILL.md` and `contracts/` as API-like artifacts.

## Required Before Merge

1. Update `VERSION` and `CHANGELOG.md` when behavior/contract changes.
2. Keep `SKILL.md`, `contracts/`, and `references/` consistent.
3. Add or update a scenario under `contracts/scenarios/` for behavior changes.

## Authoring Rules

- Use RFC2119 keywords for required behavior.
- Keep call order clear and explicit.
- Do not introduce ambiguous error handling; map to `contracts/error-taxonomy.yaml`.
- Avoid prose-only requirements when a contract field can encode them.
- Keep dependency-failure guidance current: if MCP is disconnected, agent must return connection instructions instead of attempting mutations.
- Keep capability-gap guidance current: if required capability is missing, agent must propose reusable integration options and request explicit user choice.
- Keep user-facing decision templates synchronized across `SKILL.md`, contracts, and `agents/openai.yaml`.

## Review Checklist

- Does this change alter required inputs?
- Does this change alter tool call order?
- Does this change alter output contract fields?
- Are retry/stop conditions still clear?
- Are examples and scenarios still valid?
