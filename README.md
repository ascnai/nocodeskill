# MCP Control Skill

`mcpcontrol` is a standalone skill repository for safe, deterministic operation of ASCN workflow lifecycle through the workspace MCP gateway.

## Purpose

This repo defines and governs the `mcp-control-operator` skill used by agents to:

- discover workflow and handler metadata
- validate and mutate workflow configurations
- manage workflow activation and MCP tool exports
- produce auditable, deterministic execution summaries

## Standards Profile

- Contract-first: machine-readable contracts in `contracts/`
- Deterministic execution: explicit call order and retry classes
- RFC2119 language (`MUST`, `SHOULD`, `MAY`) in normative docs
- Change control: semantic versioning + changelog

## Repository Layout

- `SKILL.md`: normative operator specification
- `contracts/skill-contract.yaml`: input/output/execution contract
- `contracts/error-taxonomy.yaml`: canonical error classes and actions
- `contracts/scenarios/*.yaml`: golden behavior scenarios
- `references/`: implementation and troubleshooting references
- `agents/openai.yaml`: agent wiring for this skill

## Compatibility

- Skill contract version: `1.x`
- Requires workspace MCP gateway exposing control tools documented in `SKILL.md`

## Release Policy

- Increment `VERSION` on every contract change.
- Update `CHANGELOG.md` for every release.
- Breaking contract/toolflow changes require a major version bump.

## Non-Goals

- Runtime API implementation itself (owned by ASCN service repos)
- Authorization policy enforcement logic (documented, not implemented here)
