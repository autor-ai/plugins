# Authoring templates

Exact arguments for creating, importing and editing templates. Each section stands alone. Fields and frames are in [fields.md](fields.md); design quality is in [craft.md](craft.md).

## Contents

- Create
- Import
- Watched builds
- Edit in place
- Assets in source
- Check and publish
- Small replies

## Create

`create_template({projectId, name, type:"image"})` returns starter files and the `revision`. Types are `image`, `video`, `pdf` and `email`. `create_template({from:{templateId}})` copies a template; `as:"video"` turns an image template into a video, one frame after another.

## Import

- HTML you already have: `create_template({projectId, from:{html}})`.
- A folder or a large page: zip it, upload it with `upload_asset`, then `create_template({projectId, from:{assetId}})`.
- A picked sketch item: `create_template({from:{sketchId, itemId}})` copies an `html` or `render` item into a new draft. The item stays on the sketch.

Elements marked `data-autor-frame` and `data-autor-field` are kept as they are on import.

## Watched builds

When the person wants to watch it being built, pass `live:true` and share the link it returns. Report each stage that renders with `edit_template({templateId, expectedRevision, generation:{id, message}, replacements})`, and finish with `generation` status `"complete"` after you have previewed.

## Edit in place

1. Read the file: `get({type:"template", id, include:["files"], paths:["index.html"]})`.
2. Change it: `edit_template({templateId, expectedRevision, replacements:[{path:"index.html", search:"<h1>Old</h1>", replace:"<h1>New</h1>"}]})`.
   - Each `search` must match exactly once. On `patch_conflict`, read again and retry.
   - If someone edited the template since your read, replacements that still match merge over their change; anything else returns `revision_conflict` with the current revision.
   - Send whole `files` only for new or fully rewritten files.
3. Put every replacement, new field and frame change for one request in one call.

Editing a published template turns it back into a draft until it is published again.

## Assets in source

- Images, fonts, audio and video go in as `/assets/<id>` embed URLs: each brand kit asset's `embedUrl`, or the `embedUrl` from `upload_asset`. For example `<img src="/assets/<id>">` or `@font-face { src: url(/assets/<id>) }`.
- Brand asset ids are not URLs. Never paste base64 and never hotlink third-party files.
- Images for one template or sketch are not brand assets: upload them with the default target, every file in one `files` list. A local file gets a signed upload URL; PUT them all in one shell loop.

## Check and publish

1. `preview({kind:"template", id})` after every meaningful change. `frameIds:["all"]` shows every frame on one numbered sheet in one call.
2. Read the review context with `get({type:"review_context", id, subjectType:"template_draft"})`, compare the preview with it, and save your review with `record_design_review`.
3. `publish_template({templateId, expectedRevision})`. Only published templates can be filled and rendered.

## Small replies

`edit_template` and `publish_template` return the revision and what changed, not the field list; read `get({type:"template", id})` for the schema. On later reviews pass `knownGuideRevision` to skip brand and voice guides you already have, or `include:[]` for just the revision.
