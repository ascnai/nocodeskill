# Integration Proposal Reference

Use when capability is insufficient for requested automation.

## Goal

Propose reusable integration paths instead of one-off workflow hacks.

## Reuse-First Decision Order

1. Compose existing handlers/triggers/tools.
2. Reuse or patch existing exported tool.
3. Connect external MCP tool.
4. Build a new reusable integration.

## Capability Gap Classification

1. `missing_handler`
2. `missing_trigger`
3. `missing_auth_capability`
4. `schema_or_contract_gap`

## Integration Proposal Card (Required)

Return one or more cards following `contracts/integration-proposal-schema.yaml`.

Minimum fields:

1. `integration_name`
2. `kind`
3. `proposed_handler_id`
4. `why_reusable`
5. `params_schema`
6. `returns_schema`
7. `required_secrets`
8. `auth_model`
9. `retry_policy`
10. `acceptance_tests`
11. `reusability_scope`

## User Decision Gate

Ask the user to select one path:

1. Compose with existing capabilities
2. Connect external MCP tool
3. Build new reusable integration

Do not resume lifecycle mutations before explicit selection.

## Decision Message Templates

Use these templates when asking user to choose direction.

`compose_existing_handlers_or_tools`

```text
I can deliver this using existing platform capabilities only.
This is the fastest path and avoids new integration work.
Proceed with composition of current handlers/tools?
```

`connect_external_mcp_tool`

```text
I can connect an external MCP tool for this missing capability.
This keeps logic reusable without implementing a new internal handler.
Proceed with MCP tool connection path?
```

`build_new_reusable_integration`

```text
Current capabilities are insufficient for reliable implementation.
I propose a reusable integration `{proposed_handler_id}` with typed schema and acceptance tests.
Proceed with building this reusable integration?
```

## User Response Templates

Use these full responses when presenting options to users. Keep placeholders and structure.

### Response: Compose Existing Capabilities

```text
Capability gap assessment: no critical blocker for delivery using existing handlers/tools.

Recommended path: compose existing capabilities.
Why: fastest delivery with lowest implementation risk.

Planned steps:
1) wire existing handlers/tools into target workflow
2) run workflow validation and fix graph/schema issues
3) activate and verify expected output

Tradeoff:
- faster delivery
- may be less reusable than a dedicated integration

If you agree, reply: "use compose path".
```

### Response: Connect External MCP Tool

```text
Capability gap assessment: required capability is not available in current registry, but can be sourced via MCP.

Recommended path: connect external MCP tool.
Why: adds reusable capability without building internal handler now.

Planned steps:
1) connect MCP server/tool for workspace `{workspace_id}`
2) verify tool schema/auth and availability in control surface
3) integrate tool into workflow, validate, activate

Tradeoff:
- medium setup effort
- dependency on external MCP endpoint reliability

If you agree, reply: "connect external mcp".
```

### Response: Build New Reusable Integration

```text
Capability gap assessment: missing capability requires a dedicated reusable integration.

Recommended path: build new reusable integration.
Proposed handler: `{proposed_handler_id}`
Why reusable: `{why_reusable}`

Planned steps:
1) finalize params/returns schema and auth model
2) implement reusable handler/trigger
3) run acceptance tests and publish for reuse
4) wire into workflow, validate, activate

Tradeoff:
- highest initial effort
- best long-term reuse and consistency

If you agree, reply: "build reusable integration".
```
