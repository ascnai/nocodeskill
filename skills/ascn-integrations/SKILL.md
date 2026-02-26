---
name: ascn-integrations
version: 0.0.4
owner: platform-ai
maturity: beta
description: Guide for designing and delivering new ASCN integrations and packaging them as user plugins.
---

# ASCN Integrations

Design and deliver missing ASCN capability as reusable integrations, then package as user-facing plugins.

## When To Use

Use this skill when a workflow task cannot be completed with existing handlers/triggers/tools.

Typical triggers:

1. missing handler for a required API/service
2. missing trigger type for required event source
3. schema/contract mismatch blocks reliable workflow composition
4. required UI usability (`params_ui`, options, conditional fields) are missing

## Required Inputs

The integrator MUST collect:

1. capability gap summary (what cannot be built today)
2. target use-cases (at least one concrete workflow scenario)
3. expected input/output contract (JSON-level)
4. auth/secret requirements
5. publish target (`user` plugin first, system copy later)

Workspace scope MUST come from runtime authorization/session context (for example MCP `/mcp` bearer-token resolution), not from manual user-provided `workspace_id` input.

If workspace context or contract expectations are missing, stop and return `input_validation_error`.

## Required Tool Surface

1. `control.docs.get`
2. `control.registry.list`
3. `control.registry.details`
4. `control.registry.resolve_options`
5. `control.workflows.list`
6. `control.workflows.describe`
7. `control.workflows.validate`
8. `control.workflows.create`
9. `control.workflows.patch`
10. `control.workflows.activate`
11. `control.tools.ensure_export`
12. `control.tools.list_exports`
13. `control.plugins.create_plugin`
14. `control.plugins.validate_definition`
15. `control.plugins.update_plugin`
16. `control.plugins.get`
17. `control.plugins.list`

If required tools are unavailable, stop and return `dependency_failure`.

## Delivery Modes

### Mode A: Workflow-backed (default)

Use existing handlers to build a reusable workflow and export it through `Trigger.Tool`.

### Mode B: Native integration (new handler/trigger code)

Use only when Mode A cannot satisfy latency, auth, determinism, or protocol constraints.

Native integrations MUST still be wrapped/published as plugins for user consumption.

Mode decision gate:

1. choose Mode A first
2. choose Mode B only with explicit constraint evidence
3. if native code surfaces are unavailable in current context, return a native implementation proposal and stop before claiming implementation

## Delivery Flow

1. Discover existing capability (`control.registry.list`, `control.registry.details`, `control.tools.list_exports`).
2. Produce contract draft:
   - exact handler id (`Vendor.Action`)
   - `params_schema` (input object)
   - `returns_schema` (output object)
3. Choose delivery mode (`A` or `B`) with explicit reason.
4. Implement integration.
5. Validate workflow/config contract (`control.workflows.validate`).
   - payload MUST be wrapped as `{ "workflow": { ... } }`
6. Activate export (`control.workflows.activate`, `control.tools.ensure_export`).
7. Run plugin-definition preflight (`control.plugins.validate_definition`).
8. Bundle handlers into plugin (`control.plugins.create_plugin` then `update_plugin` if needed).
9. Verify plugin visibility with `control.plugins.list` and registry views (`control.plugins.get` for deterministic reads).
10. Return output contract payload with verification evidence.

## Plugin Packaging Rules

1. Plugin name MUST be stable and vendor/domain-scoped (e.g. `StripeOps`, `CRMHubspot`).
2. Handler names MUST be exact and collision-safe.
3. One plugin MAY contain multiple handlers if they share domain and auth model.
4. Plugin definitions SHOULD include UI metadata (`name`, `description`, `icon`, `tags`) before handoff.
5. Unwrapped `Trigger.Tool` exports MUST still be user-visible as flat `User.<Handler>` entries.
6. Wrapped/published handlers MUST be rendered as first-class plugin cards/forms with plugin metadata (`name`, `description`, `icon`) and handler `params_ui`.

## Params UI Best Practices

`params_ui` MUST be human-usable and contract-aligned.

1. Every key in `params_ui` SHOULD exist in `params_schema.properties`.
2. Localize labels/hints where possible (`en`, `ru`).
3. Prefer explicit controls:
   - `string`, `string_multiline`, `number`, `boolean`, `options`, `array`, `object`, `string_json`
4. Use conditional visibility for complex forms via `displayOptions.show`.
5. Put dangerous/advanced options behind conditional toggles.
6. Include safe defaults where predictable behavior is expected.
7. Use `options` only for selectable values; use `displayOptions.show` only for conditional visibility.
8. Keep field order identical to the execution mental model (auth -> target -> behavior -> advanced).
9. For `options_source` fields, resolve options via `control.registry.resolve_options` (never via direct DB queries).
10. Reuse existing handler `params_ui` patterns by reading `control.plugins.list` with `include_definition=true`.

Example conditional field pattern:

```json
[
  {
    "key": "auth_mode",
    "control": "options",
    "label": {"en": "Auth mode"},
    "options": [
      {"value": "api_key", "label": {"en": "API key"}},
      {"value": "oauth", "label": {"en": "OAuth"}}
    ]
  },
  {
    "key": "api_key",
    "control": "string",
    "label": {"en": "API key"},
    "displayOptions": {"show": {"auth_mode": ["api_key"]}}
  }
]
```

Minimal `params_ui` field contract (recommended):

```json
{
  "key": "string",
  "control": "string|string_multiline|number|boolean|options|array|object|string_json",
  "label": {"en": "Field label"},
  "hint": {"en": "Optional guidance"},
  "required": false,
  "default": null,
  "options": [
    {"value": "v1", "label": {"en": "Value 1"}}
  ],
  "displayOptions": {"show": {"other_key": ["match_value"]}}
}
```

Rules for this contract:

1. `options` MUST exist only when `control=options`.
2. `displayOptions.show` MUST reference keys that exist in the same `params_ui`.
3. `required=true` fields SHOULD be present in `params_schema.required`.
4. Secret-bearing fields SHOULD use hints directing users to secrets, not literal defaults.

## Conditional UI Recipes

Use these generic recipes as defaults for `displayOptions.show`.
They mirror stable patterns already used in current plugin definitions.

### Recipe 1: Dropdown controls field visibility

```json
[
  {
    "key": "mode",
    "control": "options",
    "label": {"en": "Mode"},
    "required": true,
    "options": [
      {"value": "inline", "label": {"en": "Inline"}},
      {"value": "file", "label": {"en": "File"}}
    ]
  },
  {
    "key": "file_ref",
    "control": "object",
    "label": {"en": "File"},
    "displayOptions": {"show": {"mode": ["file"]}}
  }
]
```

### Recipe 2: Boolean toggle controls section visibility

```json
[
  {
    "key": "send_body",
    "control": "boolean",
    "label": {"en": "Send Body"}
  },
  {
    "key": "body_format",
    "control": "options",
    "label": {"en": "Body Format"},
    "displayOptions": {"show": {"send_body": [true]}},
    "options": [
      {"value": "json", "label": {"en": "JSON"}},
      {"value": "raw", "label": {"en": "Raw"}}
    ]
  }
]
```

### Recipe 3: Multi-condition show (AND)

```json
[
  {
    "key": "send_body",
    "control": "boolean",
    "label": {"en": "Send Body"}
  },
  {
    "key": "body_format",
    "control": "options",
    "label": {"en": "Body Format"},
    "options": [
      {"value": "json", "label": {"en": "JSON"}},
      {"value": "formdata", "label": {"en": "Form Data"}}
    ]
  },
  {
    "key": "body",
    "control": "string_json",
    "label": {"en": "Body"},
    "displayOptions": {
      "show": {
        "send_body": [true],
        "body_format": ["json"]
      }
    }
  }
]
```

### Recipe 4: Filter dropdown options by another dropdown

```json
[
  {
    "key": "provider",
    "control": "options",
    "label": {"en": "Provider"},
    "options": [
      {"value": "openai", "label": {"en": "OpenAI"}},
      {"value": "azure", "label": {"en": "Azure"}}
    ]
  },
  {
    "key": "model",
    "control": "options",
    "label": {"en": "Model"},
    "options": [
      {
        "value": "gpt-4o-mini",
        "label": {"en": "gpt-4o-mini"},
        "displayOptions": {"show": {"provider": ["openai"]}}
      },
      {
        "value": "azure/gpt-4o-mini",
        "label": {"en": "azure/gpt-4o-mini"},
        "displayOptions": {"show": {"provider": ["azure"]}}
      }
    ]
  }
]
```

## Conditional UI Validation Checklist

Before publish/update, the integrator MUST verify:

1. Every `displayOptions.show` key exists in the same `params_ui`.
2. Comparison values match the source field type (`true` as boolean, not `"true"` string).
3. `show` comparisons use option `value`, not option `label`.
4. Controller fields appear earlier than dependent fields in `params_ui` order.
5. Hidden-by-default advanced fields have safe defaults or are optional in schema.
6. For option-level filtering, each option-level `displayOptions.show` references an existing controller key.

## Security & Secrets

1. Credentials MUST come from secrets (`={{ $secrets.name }}`), never literals.
2. `params_schema` SHOULD mark required secret-driven fields clearly.
3. Integration MUST document minimum secret set for successful invocation.

## Stop And Retry Rules

1. Retry only transient tool/runtime failures, max 3 attempts with exponential backoff.
2. Do not auto-retry schema mismatch, validation errors, or missing capability.
3. Do not claim completion if plugin visibility or activation checks are not executed.

## Output Contract

Every completion MUST include:

```json
{
  "integration": {
    "mode": "workflow|native|proposal_only",
    "capability_status": "implemented|partially_implemented|blocked",
    "handler_names": ["Vendor.Action"],
    "workflow_id": "uuid-or-null",
    "exported_tool_name": "string-or-null"
  },
  "plugin": {
    "plugin_name": "VendorOps",
    "created_or_updated": true,
    "visible_in_plugins_list": true
  },
  "verification": {
    "validated": true,
    "activated": true,
    "smoke_tested": true,
    "latest_run_status": "COMPLETED|FAILED|RUNNING|UNKNOWN",
    "run_id": "string-or-null"
  },
  "failure": {
    "class": "none|input_validation_error|dependency_failure|schema_failure|runtime_failure|native_surface_unavailable",
    "message": "string"
  },
  "open_items": [],
  "next_action": "string"
}
```

## Hand-off Back To ASCN Operator

After integration delivery:

1. return exact handler/plugin identifiers
2. return required secrets and minimal invocation payload
3. instruct caller to resume lifecycle operations with `ascn-operator`
