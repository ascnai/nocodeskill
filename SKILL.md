---
name: mcp-control-operator
version: 1.0.0
owner: platform-ai
maturity: beta
description: Deterministic workflow lifecycle and tool-export operator for ASCN workspace MCP control tools.
---

# MCP Control Operator

This document is normative. RFC2119 keywords (`MUST`, `SHOULD`, `MAY`) define required behavior.

## Mission

Use workspace MCP `control.*` tools to safely discover, validate, mutate, activate, and export workflows with deterministic, auditable outcomes.

## Required Inputs

The operator MUST obtain:

1. `workspace_id` (UUID)
2. `intent` (`create|repair|patch|export|delete|explain`)

Optional but strongly recommended:

1. `workflow_id` for patch/repair/export/delete intents
2. required integration list and secrets map
3. success criteria (expected workflow status/tool name)

If `workspace_id` is missing, the operator MUST stop before any mutation.

## Required Tool Surface

The target gateway MUST expose these tools:

1. `control.docs.get`
2. `control.registry.list`
3. `control.registry.details`
4. `control.workflows.list`
5. `control.workflows.describe`
6. `control.workflows.validate`
7. `control.workflows.create`
8. `control.workflows.patch`
9. `control.workflows.activate`
10. `control.workflows.delete`
11. `control.tools.list_exports`
12. `control.tools.ensure_export`

If required tools are unavailable, the operator MUST fail fast with a dependency error summary.

## Deterministic Execution Policy

### Global Rules

1. The operator MUST call `control.docs.get` before intent-specific mutations.
2. The operator MUST validate before every create/patch mutation.
3. The operator MUST mutate by `workflow_id`, never inferred names.
4. The operator MUST not perform delete without explicit `confirm=true`.
5. The operator MUST run `control.workflows.activate` after successful create/patch/export.

### Intent Flows

`create`

1. `control.docs.get`
2. `control.workflows.list`
3. `control.registry.list`
4. `control.registry.details`
5. `control.workflows.validate`
6. `control.workflows.create`
7. `control.workflows.activate`

`patch|repair`

1. `control.docs.get`
2. `control.workflows.list`
3. `control.workflows.describe`
4. `control.registry.details`
5. `control.workflows.validate`
6. `control.workflows.patch`
7. `control.workflows.activate`

`export`

1. `control.docs.get`
2. `control.workflows.describe`
3. `control.tools.list_exports`
4. `control.tools.ensure_export`
5. `control.workflows.validate`
6. `control.workflows.activate`

`delete`

1. `control.workflows.describe`
2. summarize destructive impact
3. `control.workflows.delete` with `confirm=true`

## Idempotency and Retry

1. Mutation operations MUST use a deterministic operation key: 
`{workspace_id}:{intent}:{workflow_id|workflow_name}:{payload_hash}`.
2. Transient failures (`timeout`, `5xx`, gateway unavailable) MAY retry up to 3 attempts with exponential backoff.
3. Validation/context/export-conflict failures MUST NOT auto-retry; patch context/payload first.
4. Retry behavior MUST be recorded in the final output.

## Authoring Standards

1. Activity IDs MUST be unique.
2. Every `edges[].to` MUST reference an existing activity.
3. Trigger entry edges SHOULD be explicit for deterministic starts.
4. `$json` MUST be used only for current node input.
5. Upstream reads MUST use `$node['id'].json.field` with graph reachability.
6. Dynamic expressions and secrets MUST use `={{ ... }}`.
7. Credentials MUST NOT be hardcoded.

## Error Handling Standard

The operator MUST map errors to `contracts/error-taxonomy.yaml`.

Mandatory handling classes:

1. `validation`: patch payload and re-validate.
2. `context`: correct workspace/workflow mismatch before proceeding.
3. `export_conflict`: list exports and reconcile canonical name/output path.
4. `transient`: bounded retries with backoff.

## Output Contract

Every completion MUST include this shape:

```json
{
  "operations_executed": [
    {
      "step": 1,
      "tool": "control.docs.get",
      "result": "success",
      "duration_ms": 12
    }
  ],
  "final_state": {
    "workflow_id": "<uuid>",
    "version": 3,
    "status": "ACTIVE"
  },
  "validation_summary": {
    "valid": true,
    "issue_count": 0
  },
  "unresolved_risks": []
}
```

On failure, output MUST include:

1. `failing_operation`
2. `error_code` (taxonomy-aligned)
3. `error_message`
4. `next_action`

## Observability and Audit Fields

The final summary MUST include:

1. `workspace_id`
2. `intent`
3. `tool_sequence`
4. `total_duration_ms`
5. `retry_count`
6. `mutation_count`

## Mutation Safety

1. Before delete, operator MUST provide impact summary.
2. After every mutation, operator MUST report affected `workflow_id`, `version`, `status`.
3. If activation fails, operator MUST stop and provide concrete patch plan.

## Consistency Requirements

1. `SKILL.md` MUST remain consistent with `contracts/skill-contract.yaml`.
2. Scenario files in `contracts/scenarios/` SHOULD cover create, repair, and export flows.

## Change Management

1. Contract/toolflow changes MUST update `VERSION` and `CHANGELOG.md`.
2. Breaking changes MUST increment major version.
3. Non-breaking behavior additions SHOULD increment minor version.

## References

1. `references/workflow-construction.md`
2. `references/troubleshooting.md`
3. `contracts/skill-contract.yaml`
4. `contracts/error-taxonomy.yaml`
