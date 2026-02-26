# Plugin Publishing Reference

Use this reference when a user asks to package handlers into a user plugin for direct user-facing plugin usage.

## Scope

This flow publishes **plugin definitions** (handler bundles), not new handler code.

Publishing is user-facing:

1. submit in workspace via MCP control tool
2. unwrapped `Trigger.Tool` exports are shown as flat `User.<Handler>` entries in user category
3. wrapped handlers (published into plugin) are shown as first-class plugin cards/forms in user category
4. user tests plugin as a plugin card/form flow (not raw handlers)

## Required MCP Tools

1. `control.plugins.create_plugin`
2. `control.plugins.validate_definition`
3. `control.plugins.update_plugin`
4. `control.plugins.get`
5. `control.plugins.list`
6. `control.registry.resolve_options` (for dynamic `options_source`)

## Submission Flow (Workspace Side)

1. collect `workspace_id`, `plugin_name`, and handler IDs
2. call `control.registry.details` for every handler to confirm IDs and schemas
3. call `control.plugins.list` with `include_definition=true` to inspect existing handler `params_ui` examples
4. build plugin definition payload
5. for fields with `options_source`, validate values via `control.registry.resolve_options`
6. run preflight with `control.plugins.validate_definition`
7. call `control.plugins.create_plugin`
8. if edits are needed, call `control.plugins.update_plugin`
9. use `control.plugins.get` for deterministic read-back by `definition_id`
10. verify plugin visibility in `user` category (`control.plugins.list`) and provide test steps

## Control Tool Shapes

`control.plugins.create_plugin`

Required:

1. `plugin_name` (string)
2. `handlers` (array of handler IDs matching `User.<Handler>`)

Optional:

1. `plugin_definition` (preferred JSON object merged into plugin payload)
2. `definition` (legacy alias)

`control.plugins.update_plugin`

Required:

1. `definition_id`

Optional:

1. `handlers` (replaces `definition.handlers`)
2. `plugin_definition` (preferred JSON patch merge)
3. `definition` (legacy alias)

## Definition Structure (Recommended)

```json
{
  "origin": "user",
  "category": "tool",
  "name": {
    "en": "Customer Ops",
    "ru": "Операции клиентов"
  },
  "description": {
    "en": "Internal customer operations toolkit",
    "ru": "Внутренний набор операций с клиентами"
  },
  "description_full": {
    "en": "Bundle of customer lifecycle handlers used by support and risk workflows.",
    "ru": "Пакет обработчиков жизненного цикла клиента для процессов поддержки и риска."
  },
  "tags": ["internal", "customer", "ops"],
  "handlers": [
    { "handler_name": "User.FetchCustomer" },
    { "handler_name": "User.UpsertCustomer" }
  ]
}
```

## Handler-Level Params UI (Best Practices)

For each `handlers[]` item, provide localized UX metadata and `params_ui`:

```json
{
  "handler_name": "User.FetchCustomer",
  "ui_name": { "en": "Fetch Customer", "ru": "Получить клиента" },
  "ui_description": {
    "en": "Fetch customer profile by external ID.",
    "ru": "Получает профиль клиента по внешнему ID."
  },
  "ui_description_full": {
    "en": "Uses workflow-backed integration and returns normalized customer fields for downstream steps.",
    "ru": "Использует workflow-backed интеграцию и возвращает нормализованные поля клиента для следующих шагов."
  },
  "params_ui": [
    {
      "key": "customer_id",
      "control": "string",
      "label": { "en": "Customer ID", "ru": "ID клиента" },
      "hint": { "en": "External customer identifier", "ru": "Внешний идентификатор клиента" }
    },
    {
      "key": "include_contacts",
      "control": "string",
      "label": { "en": "Include Contacts", "ru": "Включать контакты" },
      "hint": {
        "en": "true/false flag for contact expansion.",
        "ru": "Флаг true/false для расширения контактных данных."
      }
    }
  ]
}
```

Validation rule:

1. `control.plugins.create_plugin` and `control.plugins.update_plugin` accept only user-defined handlers in `User.<Handler>` format.
2. Reject non-user handlers (for example `Telegram.SendMessage`, `HttpRequest.Request`).

Params UI quality rules:

1. every field MUST have stable `key` matching params schema path
2. every user-facing text SHOULD include `en` and `ru`
3. use `options` for enums; avoid free-form strings for constrained values
4. keep labels short; put guidance into `hint`
5. mark only truly mandatory fields as required
6. do not expose secrets as plain text defaults
7. keep ordering consistent with real execution order
8. avoid duplicated keys and ambiguous names
9. use `displayOptions.show` only for conditional visibility (not enum option lists)

## Plugin-Level Metadata Best Practices

1. set `origin=user` for workspace-submitted plugins
2. choose `category` intentionally (`tool`, `logic`, `ai-agent`, `trigger`)
3. use concise `name.en` and descriptive `description.en`
4. use `description_full` for operational constraints and expected outputs
5. add tags for discoverability (team, domain, provider, risk)

## Operator Response Requirements

After submit/update, operator SHOULD return:

1. `definition_id`
2. `plugin_name`
3. handler list
4. explicit note that plugin is expected under `user` category
5. any Params UI quality warnings detected before submission
