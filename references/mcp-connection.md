# MCP Gateway Connection Reference

Use this when skill is installed but MCP control tools are unavailable.

## Target

Connect agent runtime to ASCN workspace MCP gateway:

- transport: `streamable_http`
- url: `{base_url}/v1/workspaces/{workspace_id}/mcp`
- dependency id: `workspace-mcp-gateway`

## Required Inputs

1. `base_url` (ASCN API host)
2. `workspace_id` (UUID)
3. access token (if deployment requires auth)

## Verification Steps

1. Resolve final URL: `{base_url}/v1/workspaces/{workspace_id}/mcp`.
2. Configure MCP transport as `streamable_http`.
3. Add header `Authorization: Bearer <token>` if auth enabled.
4. Reconnect MCP client/session.
5. Verify control tool surface is present.

## Expected First Check

After reconnect, first successful call should be one of:

1. `control.docs.get`
2. tool discovery/list in agent runtime showing `control.*`

## Failure Escalation

If still unavailable:

1. verify workspace ID and base URL are from same environment
2. verify network route/firewall to ASCN API
3. verify token scope/policy if auth enforced
4. return dependency failure summary with error code and next action
