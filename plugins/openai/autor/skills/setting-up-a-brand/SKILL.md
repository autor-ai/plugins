---
name: setting-up-a-brand
description: Sets up or repairs a brand kit in autor. Creates a project from a company's website domain, imports its logos, colors and fonts, then checks and corrects the kit with the person. Use when someone starts with autor or has just connected it, adds a brand, client or product line, or says autor has the wrong logo, colors, fonts or tone of voice.
license: MIT
compatibility: Requires the autor MCP server at https://autor.ai/mcp.
---

# Setting up a brand in autor

Everything autor makes follows the project's brand kit: its logos, colors, fonts, design guidance and tone of voice. Get the kit right before designing anything.

The tools named here are autor's MCP tools; your host may show them with a prefix.

## 1. Connect and find the project

1. Call `get_context()`. It confirms the connection and returns the organization, the working project, recent projects and any unfinished operations.
2. If a project for this brand already exists, work in it: `get_context({projectId})` makes it the working project. Don't create a duplicate.
3. For a new brand you need its public website domain (`acme.com`), not the company name. If you only have a name, find the official website and confirm it with the person when there is any doubt.
4. `create_project({name, domain})` creates the project and starts importing the brand from the website in the background.

For an existing project whose kit is still empty, start the import with `import_brand({projectId, domain})`.

## 2. Wait for the import

The import returns an operation. Tell the person it is running, then poll `get({type:"operation", id, waitMs:20000})` until it is `completed` or `failed`. On `failed`, read the error and offer to set the kit up by hand instead.

Imports only run while the kit is empty. Once it has content, edit it directly.

When the person wants to approve the import before it applies, pass `import_brand({projectId, domain, sections:[]})`. That keeps a proposal: read it from `get({type:"brand_kit", id: projectId})` under `import.pending`, then apply the parts they accept with `update_brand_kit({projectId, proposal:{action:"apply", importRevision, sections:["design","fonts"]}})`.

## 3. Check what came back

Read `get({type:"brand_kit", id: projectId})` and review it as a designer would:

- [ ] **Logo**: the real brand mark, not a favicon, a partner logo or a cropped screenshot. Ideally one version for light and one for dark backgrounds.
- [ ] **Colors**: a clear primary, supporting and neutral colors, no near-duplicates, and readable text-on-background pairs.
- [ ] **Type**: heading and body families with their font files. A family without files renders in a fallback.
- [ ] **Voice**: tone-of-voice guidance. If it is empty, offer to draft it from the website's copy for the person to approve.

Give the person a short summary (logo, palette with hex values, fonts, voice in one sentence) and ask what is wrong or missing. Brand decisions are theirs: never guess at them silently.

## 4. Fix it

- **Tokens**: `update_brand_kit({projectId, expectedRevision, designTokens:{colors:{primary:"#0A84FF"}}})`. Tokens merge by group, and `null` removes one.
- **Identity and voice**: the same tool with `title`, `description`, `slogan` or `voice`. Send only the fields that change.
- **Logos, images and font files**: `upload_asset({target:"brand", projectId, kind:"logo", sourceUrl})`. Send several at once in `files`. For a local file, omit `sourceUrl` to get a signed upload URL.
- `fonts` and `assets` replace their whole lists, so read the kit first and send the complete list.
- Always pass the kit's `revision` from your latest read as `expectedRevision`. On `revision_conflict`, read the kit again and reapply your change.
- Turn on `enforcement` only when the person asks for brand enforcement: it makes every template need a saved design review before it can be published.

`get_guide({topic:"brand"})` has the exact fields.

## 5. Hand off

Summarize what is in the kit and what you changed. Then offer a next step: explore visual directions on a sketch, or build a first template.

## Ground rules

- People stay in charge of their brand. Ask before replacing anything they set themselves.
- Never ask for passwords or other credentials in chat.
- When a tool fails and its recovery hint doesn't help, call `send_feedback({type:"bug", summary, tool, requestId})` with the IDs involved, not the person's content.
