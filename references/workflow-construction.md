# Workflow Construction Reference

Use this checklist before `control.workflows.validate`.

## Graph Rules

1. Include `name`, `version`, and `activities`.
2. Keep all activity ids unique.
3. Ensure every edge target exists.
4. For deterministic starts, set `triggers[].edges`.
5. For deterministic execution, set activity `edges` by `when` branch.
6. If a node reads `$node['x']`, ensure path `x -> ... -> current` exists.

## Expression Rules

1. Wrap dynamic values in `={{ ... }}`.
2. Use `$json` for current node input.
3. Use `$node['id'].json.field` for upstream node outputs.
4. Use `={{ $secrets.secret_name }}` for credentials.

## Minimal Deterministic Example

```json
{
  "name": "example_linear",
  "version": 1,
  "triggers": [
    {
      "id": "cron",
      "type": "Trigger.Cron",
      "params": {
        "spec": "0 0 * * * *"
      },
      "edges": [
        {
          "to": "build"
        }
      ]
    }
  ],
  "activities": [
    {
      "id": "build",
      "type": "JS.Run",
      "params": {
        "source": "return { text: 'hello' };"
      },
      "edges": [
        {
          "to": "done",
          "when": "success"
        }
      ]
    },
    {
      "id": "done",
      "type": "Control.Success",
      "params": {
        "message": "ok"
      }
    }
  ]
}
```

## Telegram Pattern

Use this shape for downstream send step:

```json
{
  "id": "send",
  "type": "TelegramBot.SendMessage",
  "params": {
    "bot_token": "={{ $secrets.telegram_bot_token }}",
    "chatId": "={{ $secrets.telegram_chat_id }}",
    "resource": "message",
    "operation": "sendMessage",
    "text": "={{ $node['format'].json.text }}"
  }
}
```

Do not use raw `$secrets.foo` without expression wrapper.
