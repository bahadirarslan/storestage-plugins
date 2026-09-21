# StoreStage plugin

This package connects Codex and Claude Code to the hosted StoreStage Remote MCP endpoint. It does not bundle, proxy, or modify the MCP server.

## Included

- `.mcp.json`: shared remote HTTP MCP configuration
- `.codex-plugin/plugin.json`: Codex manifest and marketplace presentation metadata
- `.claude-plugin/plugin.json`: Claude Code manifest
- `assets/`: approved StoreStage symbol and light/dark app-icon masters
- `skills/storestage-workflows/SKILL.md`: client-neutral workflow guidance

The MCP server uses OAuth 2.1 with PKCE and Dynamic Client Registration. Authentication happens in the browser; users do not need to create or paste a client ID or API token.

## Requirements

- A StoreStage account with the entitlement required by the requested operation
- A plugin-capable Codex or Claude Code release
- Network access to `https://mcp.storestage.studio/mcp`

## Security model

- No API keys or OAuth tokens are stored in the package.
- Project mutations use StoreStage's existing preview/apply and revision checks.
- Asset and export bytes use short-lived signed URLs rather than MCP payloads.
- Server-side plan, scope, quota, and account-isolation checks remain authoritative.
