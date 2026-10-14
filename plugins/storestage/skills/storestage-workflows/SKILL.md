---
name: storestage-workflows
description: Use StoreStage Remote MCP to create, inspect, edit, localize, render, and export App Store screenshot projects, and to upload them to App Store Connect. Use when the user asks to work with StoreStage, App Store screenshot sets, StoreStage themes, or StoreStage cloud projects.
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
- To change the copy of several outputs, send one `updateOutputCopy` operation with `index` as a list (`[0, 1, 2]`) or `"all"` instead of one operation per output. Each output still counts against the 30-operation limit; `"all"` skips outputs the patch cannot change and lists them in a warning.
- Check a theme's layout kind: `kind` in `themes.search` and `themes.get`, `themeKind` in `sets.list`. In a `connected` theme the panels form one continuous strip, so a copy or layer change on one panel can unbalance its neighbours: read the `connected_strip_rhythm` warning in the preview and keep the rhythm. Panels of a `panel` theme are independent.
- For assets, request a signed upload target with `assets.create_upload`, then attach the uploaded asset to the intended slot with `assets.attach`. Do not put file bytes or base64 in MCP tool arguments.
- For previews and exports, start the asynchronous job, poll its matching `get` tool until it reaches a terminal state, and use `exports.download` only after a successful export.
- To send a finished set to App Store Connect, work in this order: `app_store_connect.get_status` (are credentials configured?), `app_store_connect.list_apps` (find the app, an editable version, and the localization), `exports.create` and poll `exports.get` until the job succeeds, then `app_store_connect.upload_screenshots` with that job's `jobId`. Only a succeeded, clean (not watermarked) export can be uploaded, the target version must be editable, and an approval cannot outlive its export: if the export is about to expire, export again. These tools need the `storestage:app_store_connect` scope, which a client may not have been granted; uploading also needs the export scope.

## Preserve user intent

- Do not silently switch the active screenshot set. Pass `setId` for set-scoped operations when the user identified a set.
- Treat project revision conflicts as a request to re-read the project and re-preview the intended edit. Never force an outdated change set through.
- Keep theme choice, copy changes, asset binding, localization, rendering, and export as separate decisions when the user's request is ambiguous.
- A person must approve every App Store Connect upload in StoreStage. Call `app_store_connect.upload_screenshots` without `confirmed` first: it returns the upload plan and an `approvalUrl`. Show the plan and the link to the user and wait. Call it again with `confirmed: true` and the `approvalId` only after the user says they approved it. Never set `confirmed: true` on your own, and never try to approve on the user's behalf.
- `replaceExisting` defaults to `true`, which clears the screenshots already in the target App Store Connect set before uploading. Say so in the plan and set `replaceExisting: false` when the user wants to keep them. An upload targets one app version localization and one device at a time.
- Report entitlement, missing-scope, quota, and configuration errors plainly. Do not retry an operation that requires a plan upgrade or a missing server-side feature.

## Finish with useful results

Return the project or job identifier, what changed, whether the change was previewed or applied, and the next action. For render/export jobs, include the final status and download URL when the server provides one.
