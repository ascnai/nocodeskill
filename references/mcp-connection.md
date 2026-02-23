# MCP Gateway Connection Reference

Use this when skill is installed but MCP control tools are unavailable.

## Target

Connect agent runtime to ASCN workspace MCP gateway:

- transport: `streamable_http`
- url: `https://dev-nocode.ascn.ai/mcp`
- dependency id: `workspace-mcp-gateway`

## Required Inputs

1. ASCN API URL: `https://dev-nocode.ascn.ai/mcp`
2. `workspace_id` (UUID)
3. workspace secret name `mcp_gateway_token`
4. access token (must match secret value)

## Verification Steps

1. Resolve final URL: `https://dev-nocode.ascn.ai/mcp`.
2. Configure MCP transport as `streamable_http`.
3. Create/update workspace secret `mcp_gateway_token` with target token.
4. Add header `Authorization: Bearer <token>` using the same value.
5. Reconnect MCP client/session.
6. Verify control tool surface is present.

## Expected First Check

After reconnect, first successful call should be one of:

1. `control.docs.get`
2. tool discovery/list in agent runtime showing `control.*`

## Failure Escalation

If still unavailable:

1. verify workspace ID and base URL are from same environment
2. verify network route/firewall to ASCN API
3. verify `mcp_gateway_token` secret value matches bearer header
4. return dependency failure summary with error code and next action
