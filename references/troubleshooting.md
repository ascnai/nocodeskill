# Troubleshooting Reference

Use this table with `contracts/error-taxonomy.yaml`.

## Fast Triage Order

1. Confirm MCP URL: `/v1/workspaces/{workspace_id}/mcp`.
2. Confirm workspace consistency across all calls.
3. Confirm target workflow belongs to workspace.
4. Confirm validation result before activation.

## Error Mapping

| Symptom | Taxonomy Code | Class | Action |
|---|---|---|---|
| invalid activity type | E_VALIDATION_UNKNOWN_ACTIVITY_TYPE | validation | use canonical handler from registry list/details |
| edge points to unknown node | E_VALIDATION_UNKNOWN_EDGE_TARGET | validation | fix `edges[].to` or add node |
| not reachable | E_VALIDATION_REACHABILITY | validation | add directed path between nodes |
| unsupported directive | E_VALIDATION_UNSUPPORTED_DIRECTIVE | validation | wrap with `={{ ... }}` |
| 403 / workspace mismatch | E_CONTEXT_FORBIDDEN / E_CONTEXT_WORKSPACE_MISMATCH | context | correct workspace and workflow binding |
| workflow not found in workspace | E_CONTEXT_WORKFLOW_NOT_FOUND | context | re-discover with workflows list/describe |
| duplicate export name | E_EXPORT_CANONICAL_NAME_CONFLICT | export_conflict | list exports, reconcile tool/handler |
| export outputPath invalid | E_EXPORT_INVALID_OUTPUT_PATH | export_conflict | patch output path, validate, activate |
| timeout / 5xx | E_TRANSIENT_TIMEOUT / E_TRANSIENT_UPSTREAM_5XX | transient | retry with bounded backoff |

## Repair Sequence

1. `control.docs.get`
2. `control.workflows.list`
3. `control.workflows.describe`
4. `control.workflows.validate`
5. `control.workflows.patch`
6. `control.workflows.activate`

## Export Repair Sequence

1. `control.workflows.describe`
2. `control.tools.list_exports` (use `include_invalid=true` when needed)
3. `control.tools.ensure_export`
4. `control.workflows.validate`
5. `control.workflows.activate`

## Stop Conditions

Stop and return failure summary when:

1. required tool surface is missing
2. workspace/workflow context cannot be resolved
3. validation errors persist after attempted patching
4. destructive action lacks explicit confirmation
