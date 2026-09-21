---
name: storestage-workflows
description: Use StoreStage Remote MCP to create, inspect, edit, localize, render, and export App Store screenshot projects. Use when the user asks to work with StoreStage, App Store screenshot sets, StoreStage themes, or StoreStage cloud projects.
---

# StoreStage workflows

Use the `storestage` MCP server. It is a remote HTTP server protected by OAuth; never ask the user to paste an API key or access token into chat.

## Start safely

1. If StoreStage tools are unavailable or authentication is required, tell the user to connect through the client's MCP authentication flow.
2. Call `account.get_status` before account-specific work to confirm the plan, granted scopes, and feature availability.
3. Use read tools first to identify the exact project, screenshot set, output, theme, and current revision.

## Choose the shortest workflow

- Explore designs with `themes.search`, `themes.get`, and `themes.get_preview_image`.
- Inspect existing work with `projects.list`, `projects.get`, `sets.list`, and `outputs.get`.
- For a new project from a product brief, prefer `projects.draft_from_brief` when its inputs fit the request. Use `projects.create` when the user wants an empty or manually assembled project.
- For edits, call `changes.preview`, summarize the returned diff, and call `changes.apply` only after the user has approved the staged change or explicitly requested the complete edit.
- For localization, use `sets.translate`, review the staged translation diff, then apply the returned change set.
- For assets, request a signed upload target with `assets.create_upload`, then attach the uploaded asset to the intended slot with `assets.attach`. Do not put file bytes or base64 in MCP tool arguments.
- For previews and exports, start the asynchronous job, poll its matching `get` tool until it reaches a terminal state, and use `exports.download` only after a successful export.

## Preserve user intent

- Do not silently switch the active screenshot set. Pass `setId` for set-scoped operations when the user identified a set.
- Treat project revision conflicts as a request to re-read the project and re-preview the intended edit. Never force an outdated change set through.
- Keep theme choice, copy changes, asset binding, localization, rendering, and export as separate decisions when the user's request is ambiguous.
- Report entitlement, missing-scope, quota, and configuration errors plainly. Do not retry an operation that requires a plan upgrade or a missing server-side feature.

## Finish with useful results

Return the project or job identifier, what changed, whether the change was previewed or applied, and the next action. For render/export jobs, include the final status and download URL when the server provides one.
