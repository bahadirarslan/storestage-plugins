# StoreStage plugins

Official Codex and Claude Code plugin distribution for [StoreStage](https://storestage.studio).

This repository contains only client manifests, workflow guidance, and approved brand assets. The StoreStage application and MCP server source code are not included. The plugin connects to the hosted StoreStage Remote MCP endpoint and authenticates through OAuth 2.1 with PKCE.

## Install for Codex

```sh
codex plugin marketplace add bahadirarslan/storestage-plugins
codex plugin add storestage@storestage-plugins
```

Start a new Codex task after installation and complete the browser-based StoreStage sign-in flow.

For Codex releases that support Remote MCP but not plugins:

```sh
codex mcp add storestage --url https://mcp.storestage.studio/mcp
codex mcp login storestage
```

## Install for Claude Code

```sh
claude plugin marketplace add bahadirarslan/storestage-plugins
claude plugin install storestage@storestage-plugins --scope user
```

Start a new Claude Code session after installation. Open `/mcp` if the browser authorization flow does not start automatically.

## Security

- No API keys, OAuth tokens, application source code, or deployment configuration are stored here.
- Authentication is handled by the hosted StoreStage OAuth flow.
- Server-side plan, scope, quota, and account-isolation checks remain authoritative.

See the [StoreStage MCP Guide](https://storestage.studio/mcp-guide) for setup and usage details.

Copyright © StoreStage. All rights reserved.
