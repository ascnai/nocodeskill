# ASCN Integrations References

References for `ascn-integrations`.

## Minimal Checklists

1. Reuse-first discovery: `control.registry.list`, `control.registry.details`, `control.tools.list_exports`.
2. Validate before activation: `control.workflows.validate` with `{ "workflow": { ... } }`, then `control.workflows.activate`.
3. Plugin packaging preflight and visibility: `control.plugins.validate_definition`, `control.plugins.create_plugin`, verify in `control.plugins.list` (`control.plugins.get` for deterministic reads).
4. Connection shape: `streamable_http` with `https://nocode.ascn.ai/mcp` and workspace secret `mcp_gateway_token`.
5. MCP token source: `https://ascn.ai/no-code/mcp-list`.
