# ASCN Integrations

`ascn-integrations` defines deterministic guidance for building missing ASCN capability and packaging it as reusable plugins.

## Scope

- new integration design (workflow-backed first)
- native handler/trigger integration path when required
- `params_schema` / `returns_schema` contract discipline
- `params_ui` UX standards including conditional fields
- plugin packaging via control MCP plugin endpoints

## Companion Usage

This skill is invoked by `ascn-operator` whenever capability gaps are detected.
