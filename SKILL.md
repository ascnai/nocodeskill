---
name: mcp-control-operator
description: Professional workflow authoring and lifecycle control through workspace MCP control tools. Use when Codex or an agent must call control.registry.list, control.registry.details, control.workflows.validate/create/patch/activate/delete, control.tools.ensure_export, or control.docs.get to build, repair, export, or operate workflows via the workspace MCP gateway.
---

# MCP Control Operator

Execute this workflow to manage ASCN workflows through MCP with deterministic results.

## Required Inputs

- `workspace_id`
- task intent (create, repair, export, delete, explain)
- required integrations/secrets (if any)

If `workspace_id` is missing, ask for it before mutations.

## Operating Protocol

1. Call `control.docs.get` first.
2. Call `control.registry.list`.
3. Call `control.registry.details` for every handler you plan to use.
4. Draft workflow with explicit graph edges.
5. Run `control.workflows.validate`.
6. If invalid, patch and re-validate until `valid=true`.
7. Run `control.workflows.create` (new) or `control.workflows.patch` (existing).
8. Run `control.workflows.activate`.
9. If MCP exposure is needed, run `control.tools.ensure_export`, then validate + activate again.

Do not skip validation before mutations.

## Authoring Rules

1. Use unique activity IDs.
2. Ensure every `edges[].to` references an existing activity ID.
3. Add trigger entry edges (`triggers[].edges`) to define starting node(s).
4. Add success/failure edges between activities to define execution order.
5. Use `$json` only for current node input.
6. Use `$node['upstream_id'].json.field` for upstream outputs.
7. Ensure reachability: if node `B` references node `A`, graph must contain path `A -> ... -> B`.
8. Use `={{ ... }}` for dynamic expressions and secrets.
9. Use `={{ $secrets.name }}` for credentials; never hardcode tokens.
10. Create/patch first, then activate explicitly. Do not rely on `status` in payload.

## Mutation Safety

1. For delete, require explicit `confirm=true`.
2. Before destructive changes, summarize what will be removed and why.
3. After create/patch/export, report affected workflow id and active status.

## Error Recovery Matrix

- `invalid activity type`:
  - Re-run `control.registry.list/details` and replace type with canonical handler name.
- `edge points to unknown node`:
  - Fix `edges[].to` targets to existing IDs.
- `results.<node> not reachable from <node>`:
  - Add directed edges so referenced node can reach current node.
- `unsupported templating directive $...`:
  - Wrap expression as `={{ ... }}`.
- `bot_token is required` / `chatId is required`:
  - Populate required Telegram params, usually from `$secrets`.
- `403` during workflow operations:
  - Confirm you are using the correct workspace MCP URL: `/v1/workspaces/{workspace_id}/mcp`.
  - Confirm operation is performed in the same workspace as the workflow ID.
  - Re-run `control.workflows.validate` and ensure final graph is valid before activate.

## Call Patterns

Use these minimal argument shapes.

```json
{"names":["Trigger.Cron","JS.Run","TelegramBot.SendMessage","Control.Success"]}
```

```json
{"workflow":{"name":"example","version":1,"triggers":[],"activities":[]}}
```

```json
{"workflow_id":"<uuid>","patch":{"activities":[{"id":"a","type":"JS.Run","params":{"source":"return {ok:true};"}}]}}
```

```json
{"workflow_id":"<uuid>","tool":"Market","handler":"HourlyAnalytics","expose_mcp":true,"output_path":"out"}
```

## Output Contract

When finishing a task, always return:

1. operations executed (ordered)
2. final workflow id/version/status
3. validation result summary
4. unresolved risks (if any)

For failures, return:

1. failing operation
2. exact error message
3. concrete next patch action

## References

- Read `references/workflow-construction.md` for strict graph and expression examples.
- Read `references/troubleshooting.md` for 403 and validation failure playbooks.
