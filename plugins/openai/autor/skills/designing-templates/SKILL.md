---
name: designing-templates
description: Builds and edits autor templates, the reusable HTML designs for images, videos, PDFs and emails with editable fields and sizes. Previews them, reviews them against the brand kit and publishes them. Use when someone asks to design, build, import, change or fix a template, social post, ad, banner, flyer, newsletter or email layout in autor, or to address comments pinned on a draft template.
license: MIT
compatibility: Requires the autor MCP server at https://autor.ai/mcp.
---

# Designing templates in autor

A template is HTML and CSS (or a Remotion composition for video) plus **fields**, the parts that change for each piece of content (headline, photo, call to action), and **frames**, the sizes it renders at (for example a 1080×1080 feed post and a 1080×1920 story). Only published templates can be filled and rendered.

The tools named here are autor's MCP tools; your host may show them with a prefix.

## Before you start

1. `get_context()`, then read the brand kit: `get({type:"brand_kit", id: projectId})`. Its tokens, fonts, logo `embedUrl`s and voice are the source of truth. Don't invent colors or fonts.
2. Changing an existing template? Find it with `search({type:"template", projectId, query})` and edit that template. Copy it only when the person asks for a separate one.
3. Still deciding how it should look? Explore options on a sketch first. Never create templates just to compare ideas.

## Create or import

- **New**: `create_template({projectId, name, type:"image"})` returns starter files and the `revision`. Types are `image`, `video`, `pdf` and `email`.
- **Import HTML**: `create_template({projectId, from:{html}})`, or upload an `.html` or `.zip` with `upload_asset` and pass `from:{assetId}`.
- **From a sketch**: `create_template({from:{sketchId, itemId}})` turns a picked sketch item into a draft.
- **Watched build**: with `live:true` the person watches it being built. Share the link it returns, report each stage through `edit_template` with `generation:{id, message}`, and finish with `generation.status` `"complete"`.

## The edit loop

1. Read the source: `get({type:"template", id, include:["files"], paths:["index.html"]})`.
2. Change it with `edit_template({templateId, expectedRevision, replacements:[{path:"index.html", search:"<h1>Old</h1>", replace:"<h1>New</h1>"}]})`. Each `search` must match exactly once; on `patch_conflict`, read again and retry. Send whole `files` only for new or fully rewritten files.
3. Batch: put every replacement, new field and frame change for one request into a single `edit_template` call. Some hosts ask the person to confirm each write, so ten small calls means ten confirmations.
4. Look at it: `preview({kind:"template", id})` after every meaningful change, or `frameIds:["all"]` to see every frame on one numbered sheet in one call (`timestamps:[0,2,4]` does the same for video). Fix overflow, clipping, low contrast and misalignment before you show anything.

Send the `revision` from each response as `expectedRevision` on the next write. If someone edited the template since, `replacements` that still match merge over their change; other edits return `revision_conflict` with `details.currentRevision`.

## Conventions that matter

- **Images and fonts** go into source only as `/assets/<id>` embed URLs, from brand kit assets or `upload_asset`. Never paste base64, never use brand asset ids as URLs, never hotlink third-party files.
- **Fields**: `addFields:[{label:"Headline", type:"text", selector:"h1"}]`. A field binds every element its selector matches, in every frame, and takes the first match's current content as its default.
- **Frames**: mark each size's element with `data-autor-frame="<id>"`, or set the list with `frames`.
- **Real copy**: design for the longest realistic text and the widest image crops, not the placeholder. Use CSS that adapts (line clamping, fitted type) so a long headline never overflows.

Field types, choices, rich text, image crops, lists and frames are in [references/fields.md](references/fields.md).

## Review and publish

1. Read the review context: `get({type:"review_context", id, subjectType:"template_draft"})`.
2. Check the rendered preview against its design and voice guidance, then save your review with `record_design_review({subjectType:"template_draft", subjectId, expectedRevision, findings})`. Empty `findings` means compliant. Use `blocking` only for explicit brand rules.
3. Publish with `publish_template({templateId, expectedRevision})`. When brand enforcement is on, publishing needs a current review. Blocking findings need an admin's `overrideReason`: never send one unless that admin tells you to.

Editing a published template turns it back into a draft until it is published again.

## Comments from people

People pin comments to draft templates. `get({type:"template", id})` returns the open ones with the field and element under each pin. Fix them in one `edit_template` call, passing `resolveComments:[{id, note}]` with a short note for each, then preview.

## Report back

Show the final preview (call `show` when your host offers it), then list the editable fields, the frames, and anything you weren't sure about.

`get_guide({topic:"authoring"})` has the exact arguments. When a tool fails and its recovery hint doesn't help, call `send_feedback` with the tool and `requestId`.
