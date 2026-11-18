# Brand kit fields

Exact arguments for importing and editing a brand kit. Each section stands alone.

## Import from a website

- `create_project({name, domain:"acme.com"})` creates a project and imports its brand; `import_brand({projectId, domain})` imports into an existing project. Both run in the background: poll `get({type:"operation", id, waitMs:20000})`.
- You need a domain, not a company name. If you only have a name, find the official website first.
- The import applies identity, design, assets and fonts by default. Pass `sections:[]` to keep it as a proposal instead, read it under `import.pending` in `get({type:"brand_kit", id: projectId})`, then apply the parts the person accepts with `update_brand_kit({projectId, proposal:{action:"apply", importRevision, sections:["design","fonts"]}})`.
- A website import only runs while the kit is empty. Afterwards, edit fields directly.

## Edit the kit

`update_brand_kit({projectId, expectedRevision, title, description, voice, designTokens})`, sending only what changes.

- `designTokens` merge by group, and `null` removes a token: `{colors:{primary:"#0A84FF"}, typography:{heading:{fontFamily:"Inter", fontSize:"48px"}}}`.
- `fonts` and `assets` replace their whole lists: read the kit first and send the complete list.
- Pass the kit's `revision` from your latest read as `expectedRevision`. On `revision_conflict`, read again and reapply your change.

## Logos, images and font files

`upload_asset({target:"brand", projectId, kind:"logo", sourceUrl})` adds one to the kit. Send several at once in `files`. For a local file, leave out `sourceUrl` to get a signed upload URL.

## Enforcement

`enforcement:true` makes every template in the project need a saved design review before it can be published. Turn it on only when the person asks for brand enforcement.
