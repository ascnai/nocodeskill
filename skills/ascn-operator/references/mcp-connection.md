# MCP Gateway Connection Reference

Use this when skill is installed but MCP control tools are unavailable.

## Target

Connect agent runtime to ASCN workspace MCP gateway:

- transport: `streamable_http`
- url: `https://nocode.ascn.ai/mcp`
- dependency id: `workspace-mcp-gateway`

## Required Inputs

1. ASCN base URL
2. workspace secret name `mcp_gateway_token`
3. access token (must match secret value)
4. token generation URL: `https://ascn.ai/no-code/mcp-list`

## Verification Steps

1. Resolve final URL: `https://nocode.ascn.ai/mcp`.
2. Configure MCP transport as `streamable_http`.
3. Open `https://ascn.ai/no-code/mcp-list` and generate or copy the target token.
4. Create/update workspace secret `mcp_gateway_token` with that token.
5. Add header `Authorization: Bearer <token>` using the same value.
6. Reconnect MCP client/session.
7. Verify control tool surface is present.

## Expected First Check

After reconnect, first successful call should be one of:

1. `control.docs.get`
2. tool discovery/list in agent runtime showing `control.*`

## Failure Escalation

If still unavailable:

1. verify base URL matches the intended environment
2. verify network route/firewall to ASCN API
3. verify `mcp_gateway_token` secret value matches bearer header
4. verify token from `https://ascn.ai/no-code/mcp-list` is the one stored in the secret and header
5. return dependency failure summary with error code and next action
