# ASCN Operator

`ascn-operator` is a skill in the `mcpcontrol` multi-skill repository for safe and reliable ASCN workflow lifecycle operations through the workspace MCP gateway.

## Purpose

This repo defines and governs the `ASCN operator` skill used by agents to:

- discover workflow and handler metadata
- validate and mutate workflow configurations
- manage workflow activation and MCP tool exports
- submit user plugin bundles for user-category plugin usage and testing
- verify exported tool runtime behavior through workflow run history
- produce clear and auditable execution summaries

## Standards Profile

- Contract-first: machine-readable contracts in `contracts/`
- Clear execution model: explicit call order and retry classes
- RFC2119 language (`MUST`, `SHOULD`, `MAY`) in normative docs
- Change control: semantic versioning + changelog

## Repository Layout

- `SKILL.md`: normative operator specification
- `contracts/skill-contract.yaml`: input/output/execution contract
- `contracts/error-taxonomy.yaml`: canonical error classes and actions
- `contracts/integration-proposal-schema.yaml`: schema for reusable integration proposals
- `contracts/scenarios/*.yaml`: golden behavior scenarios
- `references/`: implementation, troubleshooting, and connection references
- `agents/openai.yaml`: agent wiring for this skill

## Compatibility

- Skill contract version: `0.0.1`
- Requires workspace MCP gateway exposing control tools documented in `SKILL.md`
- Includes plugin submission tools:
  - `control.plugins.create_plugin`
  - `control.plugins.update_plugin`

## MCP Connection Prerequisite

If the skill is present but MCP is not connected, agents must stop mutations and return connection steps to user.

Connection shape:

1. transport: `streamable_http`
2. URL: `https://nocode.ascn.ai/mcp`
3. dependency id: `workspace-mcp-gateway`
4. workspace secret name: `mcp_gateway_token`
5. auth header: `Authorization: Bearer <token>` (must match secret value)
6. token source: `https://ascn.ai/no-code/mcp-list`

## Capability Gap Strategy

When required handler/trigger/tool capability is missing, agent must:

1. classify the gap type,
2. propose reuse-first options,
3. provide reusable Integration Proposal Card(s),
4. wait for explicit user decision before lifecycle mutations.

Decision responses are standardized with templates for:

1. composing existing capabilities,
2. connecting external MCP tool,
3. building new reusable integration.

## Runtime Traceability

For exported MCP tools, agents must:

1. perform a smoke invocation,
2. inspect latest workflow runs via `control.runs.list`,
3. inspect full node outputs/timeline via `control.runs.details` when run fails,
4. include `run_id` and `trace_id` in failure summaries when execution fails.

## Data Reference Syntax

Agent authoring must use explicit expression wrappers:

1. current input: `={{ $json.field }}`
2. upstream node: `={{ $node['id'].json.field }}`
3. secrets: `={{ $secrets.secret_name }}`

Raw `$node[...]` and `$json...` strings without `={{ ... }}` are treated as invalid directives in strict validation paths.

## Release Policy

- Increment `VERSION` on every contract change.
- Update `CHANGELOG.md` for every release.
- Breaking contract/toolflow changes require a major version bump.

## Non-Goals

- Runtime API implementation itself (owned by ASCN service repos)
- Authorization policy enforcement logic (documented, not implemented here)
