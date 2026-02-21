# Troubleshooting Reference

## 403 During Agent Workflow Operations

Check in this order:

1. MCP URL is `/v1/workspaces/{workspace_id}/mcp`.
2. The same `workspace_id` is used across all control calls.
3. Target `workflow_id` belongs to that workspace.
4. Workflow is valid before activation (`control.workflows.validate`).

If issue persists, re-run with explicit sequence:

1. `control.docs.get`
2. `control.workflows.validate`
3. `control.workflows.patch` (fixes)
4. `control.workflows.activate`

## Common Validation Failures

### Unknown activity type

- Cause: non-canonical handler name.
- Fix: `control.registry.list`, then `control.registry.details`, then update `type`.

### Unknown edge target

- Cause: `edges[].to` references missing activity id.
- Fix: correct target ids or add missing node.

### Reachability error

- Cause: upstream reference with no directed path.
- Fix: add connecting edges.

### Unsupported directive

- Cause: raw `$...` string where expression wrapper is required.
- Fix: use `={{ ... }}`.

## Repair Strategy for Invalid Auto-Generated Workflows

1. Build intended execution order as linear/branched plan.
2. Add trigger entry edges to first step(s).
3. Add activity edges for every transition.
4. Convert ambiguous `$json` reads to `$node[...]` where upstream data is needed.
5. Validate until no errors.
6. Activate only after clean validation.
