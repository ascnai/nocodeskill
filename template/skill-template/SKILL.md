---
name: example-skill
version: 0.1.0
owner: team-name
maturity: draft
description: One-line description of what the skill does.
---

# Example Skill

Use this template for LLM-reliable skills with explicit triggers, deterministic flow, and structured outputs.

## When To Use

Use this skill when:

1. the user asks for `<task-domain>`
2. the task requires `<specialized workflow/tooling>`
3. direct generic execution is lower quality than the guided flow below

Do not use this skill when:

1. required tools are unavailable and no fallback exists
2. the request is outside `<task-domain>`

## Required Inputs

Collect before action:

1. `<required_input_1>`
2. `<required_input_2>`

Optional but useful:

1. `<optional_input_1>`
2. `<optional_input_2>`

If required inputs are missing, stop and return `input_validation_error`.

## Required Tool Surface

1. `<tool.or.command.1>`
2. `<tool.or.command.2>`

If tools are missing, stop and return `dependency_failure`.

## Decision Flow

1. classify request type (`<type_a|type_b|type_c>`)
2. choose execution path for that type
3. run the matching path only
4. if no path applies, return `unsupported_request`

## Execution Policy

Global rules:

1. validate inputs before mutation or side effects
2. apply smallest valid change first, then expand
3. include verification evidence in output
4. never claim completion without running required checks

Execution order:

1. `<step_1>`
2. `<step_2>`
3. `<step_3>`
4. `<verification_step>`

## Retry And Stop Rules

1. retry only transient failures (`timeout`, `5xx`, transport errors), max 3 attempts with exponential backoff
2. do not auto-retry validation/schema/conflict failures
3. stop immediately on destructive actions unless explicit confirmation is present

## Output Contract

Every completion MUST include:

```json
{
  "skill": "example-skill",
  "status": "completed|partial|failed",
  "actions": [
    {"step": "string", "status": "completed|skipped|failed"}
  ],
  "verification": {
    "validated": true,
    "checked": true
  },
  "failure": {
    "class": "none|input_validation_error|dependency_failure|validation_failure|runtime_failure|unsupported_request",
    "message": "string"
  },
  "open_items": [],
  "next_action": "string"
}
```
