# Troubleshooting Reference

Use this table with `contracts/error-taxonomy.yaml`.

## Fast Triage Order

1. Confirm MCP URL: `https://nocode.ascn.ai/mcp`.
2. Confirm workspace consistency across all calls.
3. Confirm target workflow belongs to workspace.
4. Confirm validation result before activation.
5. For runtime failures, inspect latest workflow runs via `control.runs.list`.

## Error Mapping

| Symptom | Taxonomy Code | Class | Action |
|---|---|---|---|
| invalid activity type | E_VALIDATION_UNKNOWN_ACTIVITY_TYPE | validation | use handler ID from registry list/details |
| edge points to unknown node | E_VALIDATION_UNKNOWN_EDGE_TARGET | validation | fix `edges[].to` or add node |
| not reachable | E_VALIDATION_REACHABILITY | validation | add directed path between nodes |
| unsupported directive | E_VALIDATION_UNSUPPORTED_DIRECTIVE | validation | wrap with `={{ ... }}` |
| 403 / workspace mismatch | E_CONTEXT_FORBIDDEN / E_CONTEXT_WORKSPACE_MISMATCH | context | correct workspace and workflow binding |
| workflow not found in workspace | E_CONTEXT_WORKFLOW_NOT_FOUND | context | re-discover with workflows list/describe |
| duplicate export name | E_EXPORT_CANONICAL_NAME_CONFLICT | export_conflict | list exports, reconcile tool/handler |
| export outputPath invalid | E_EXPORT_INVALID_OUTPUT_PATH | export_conflict | patch output path, validate, activate |
| timeout / 5xx | E_TRANSIENT_TIMEOUT / E_TRANSIENT_UPSTREAM_5XX | transient | retry with bounded backoff |
| MCP gateway not connected | E_DEPENDENCY_MCP_NOT_CONNECTED | dependency | provide user connection runbook and stop |
| required control tool missing | E_DEPENDENCY_REQUIRED_TOOL_MISSING | dependency | reconnect MCP and verify tool surface |
| missing handler/trigger for requested flow | E_CAPABILITY_MISSING_HANDLER / E_CAPABILITY_MISSING_TRIGGER | capability_gap | propose reusable integration options and request user decision |

## Runtime Failure Trace Sequence

1. Capture `run_id` / `trace_id` from tool response when available.
2. Call `control.runs.list` for the target workflow.
3. Call `control.runs.details` for failed run IDs.
4. Inspect output nodes, timeline entries, and `trigger_meta.trace_id`.
5. Report failure summary with `run_id`, `trace_id`, and next fix action.

## MCP Not Connected: User Instruction Runbook

Provide this exact checklist to user:

1. Ensure ASCN API base URL is reachable.
2. Configure MCP connection:
   - transport: `streamable_http`
   - url: `https://nocode.ascn.ai/mcp`
3. Ensure workspace secret `mcp_gateway_token` exists and has the expected token value.
4. Add auth header with the same token: `Authorization: Bearer <token>`.
5. Obtain token from `https://ascn.ai/no-code/mcp-list`.
6. Reconnect MCP client/session.
7. Retry and verify control tools are visible.

User-facing template:

```text
MCP control gateway is not connected for workspace {workspace_id}.
Please configure:
- transport: streamable_http
- url: https://nocode.ascn.ai/mcp
- workspace secret: mcp_gateway_token = <token>
- Authorization: Bearer <token>
- token source: https://ascn.ai/no-code/mcp-list
Reconnect MCP and retry.
```

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
5. MCP gateway dependency is unavailable
6. capability gap exists and user has not selected integration path
