# ASCN Integrations References

Deterministic companion references for `ascn-integrations`.

## Minimal Checklists

1. Reuse-first discovery: `control.registry.list`, `control.registry.details`, `control.tools.list_exports`.
2. Validate before activation: `control.workflows.validate` then `control.workflows.activate`.
3. Plugin packaging visibility: `control.plugins.create_plugin` and verify in `control.plugins.list`.
4. Connection shape: `streamable_http` with `https://nocode.ascn.ai/mcp` and workspace secret `mcp_gateway_token`.
5. MCP token source: `https://ascn.ai/no-code/mcp-list`.
