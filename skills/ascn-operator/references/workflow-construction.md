# Workflow Construction Reference

Use this reference with `contracts/skill-contract.yaml`.

## Pre-Authoring

0. Confirm MCP gateway dependency is connected for this workspace.
1. Run `control.workflows.list` to detect existing candidates.
2. Run `control.workflows.describe` for any workflow you may patch.
3. Run `control.registry.details` for every planned handler.
4. If required capability is missing, switch to `references/integration-proposals.md`.

## Graph Checklist

1. Include `name`, `version`, `activities`.
2. Ensure all activity IDs are unique.
3. Ensure every edge target exists.
4. Set `triggers[].edges` to explicit entry nodes.
5. Use explicit success/failure edges for intended ordering.
6. Verify each `$node['x']` has path `x -> ... -> current`.

## Blueprint Catalog (Required Selector)

Pick one blueprint before drafting:

1. `linear`: single ordered chain.
2. `fanout`: one producer and multiple parallel consumers.
3. `conditional`: branch routing with true/false conditions.
4. `retryable_http`: network call plus explicit retry/error handling path.
5. `tool_export`: `Trigger.Tool` workflow exposed via MCP.

## Expression and Secrets Checklist

1. Dynamic values use `={{ ... }}`.
2. Current node input uses `$json` only.
3. Upstream references use `$node['id'].json.field`.
4. Secrets use `={{ $secrets.secret_name }}` only.
5. Never embed plaintext credentials in params.

## Node Reference Quick Examples

Use these exact patterns:

1. Current input field:
   - `={{ $json.user_id }}`
2. Upstream node full payload:
   - `={{ $node['fetch_user'].json }}`
3. Upstream node field:
   - `={{ $node['fetch_user'].json.email }}`
4. Upstream array item:
   - `={{ $node['list_orders'].json.items[0].id }}`

Avoid raw directives without expression wrapper:

- bad: `$node['fetch_user'].json.email`
- good: `={{ $node['fetch_user'].json.email }}`

## Validation Loop

1. `control.workflows.validate`
2. Patch payload for each reported issue.
3. Re-run `control.workflows.validate` until `valid=true`.
4. Apply mutation (`create`/`patch`) and `activate`.

## Post-Export Verification

For workflows exported as MCP tools:

1. invoke exported tool with minimal valid payload.
2. call `control.runs.list` for target `workflow_id`.
3. call `control.runs.details` for the selected run.
4. verify latest run status, output nodes, and capture `trace_id` from `trigger_meta`.
5. if failed, report `run_id`, `trace_id`, and node-level failure summary before further mutations.

## Minimal Example

```json
{
  "name": "example_linear",
  "version": 1,
  "triggers": [
    {
      "id": "manual",
      "type": "Trigger.Tool",
      "params": {
        "tool": "Example",
        "handler": "Run",
        "expose_mcp": false,
        "outputPath": "done"
      },
      "edges": [{ "to": "build" }]
    }
  ],
  "activities": [
    {
      "id": "build",
      "type": "JS.Run",
      "params": { "source": "return { text: 'hello' };" },
      "edges": [{ "to": "done", "when": "success" }]
    },
    {
      "id": "done",
      "type": "Control.Success",
      "params": { "message": "ok" }
    }
  ]
}
```

## Blueprint Example: Fanout

```json
{
  "name": "orders_fanout_notify",
  "version": 1,
  "triggers": [
    {
      "id": "tool_01",
      "type": "Trigger.Tool",
      "params": { "tool": "Orders", "handler": "Fanout", "expose_mcp": false, "outputPath": "done_01" },
      "edges": [{ "to": "build_payload_01" }]
    }
  ],
  "activities": [
    {
      "id": "build_payload_01",
      "type": "JS.Run",
      "params": { "source": "return { email: $json.email, slack: $json.slack, text: 'Order received' };" },
      "edges": [
        { "to": "notify_email_01", "when": "success" },
        { "to": "notify_slack_01", "when": "success" }
      ]
    },
    { "id": "notify_email_01", "type": "Process.Log", "params": { "template": "email sent" }, "edges": [{ "to": "done_01", "when": "success" }] },
    { "id": "notify_slack_01", "type": "Process.Log", "params": { "template": "slack sent" }, "edges": [{ "to": "done_01", "when": "success" }] },
    { "id": "done_01", "type": "Control.Success", "params": { "message": "fanout completed" } }
  ]
}
```

## Conditional Branch Example

```json
{
  "id": "route",
  "type": "JS.Run",
  "params": { "source": "return { urgent: !!$json.urgent };" },
  "edges": [
    { "to": "notify_fast", "when": "true", "cond": "={{ $json.urgent }}" },
    { "to": "notify_standard", "when": "false", "cond": "={{ !$json.urgent }}" }
  ]
}
```

## Blueprint Example: Retryable HTTP

```json
{
  "name": "orders_retryable_http_sync",
  "version": 1,
  "triggers": [
    {
      "id": "tool_01",
      "type": "Trigger.Tool",
      "params": { "tool": "Orders", "handler": "Sync", "expose_mcp": false, "outputPath": "done_01" },
      "edges": [{ "to": "prepare_request_01" }]
    }
  ],
  "activities": [
    {
      "id": "prepare_request_01",
      "type": "JS.Run",
      "params": { "source": "return { url: $json.url, method: 'POST', body: $json.payload, attempt: 1 };" },
      "edges": [{ "to": "http_request_01", "when": "success" }]
    },
    {
      "id": "http_request_01",
      "type": "HttpRequest.Request",
      "params": {
        "method": "={{ $node['prepare_request_01'].json.method }}",
        "url": "={{ $node['prepare_request_01'].json.url }}",
        "body": "={{ $node['prepare_request_01'].json.body }}"
      },
      "edges": [
        { "to": "done_01", "when": "success" },
        { "to": "fail_01", "when": "failed" }
      ]
    },
    { "id": "fail_01", "type": "Control.Failure", "params": { "message": "http sync failed" } },
    { "id": "done_01", "type": "Control.Success", "params": { "message": "http sync completed" } }
  ]
}
```
