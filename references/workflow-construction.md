# Workflow Construction Reference

Use this reference with `contracts/skill-contract.yaml`.

## Pre-Authoring

1. Run `control.workflows.list` to detect existing candidates.
2. Run `control.workflows.describe` for any workflow you may patch.
3. Run `control.registry.details` for every planned handler.

## Deterministic Graph Checklist

1. Include `name`, `version`, `activities`.
2. Ensure all activity IDs are unique.
3. Ensure every edge target exists.
4. Set `triggers[].edges` to explicit entry nodes.
5. Use explicit success/failure edges for intended ordering.
6. Verify each `$node['x']` has path `x -> ... -> current`.

## Expression and Secrets Checklist

1. Dynamic values use `={{ ... }}`.
2. Current node input uses `$json` only.
3. Upstream references use `$node['id'].json.field`.
4. Secrets use `={{ $secrets.secret_name }}` only.
5. Never embed plaintext credentials in params.

## Validation Loop

1. `control.workflows.validate`
2. Patch payload for each reported issue.
3. Re-run `control.workflows.validate` until `valid=true`.
4. Apply mutation (`create`/`patch`) and `activate`.

## Minimal Deterministic Example

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
