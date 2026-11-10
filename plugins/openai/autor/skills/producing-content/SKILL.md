---
name: producing-content
description: Produces finished files with autor. Fills published templates with copy and images, renders images, videos, PDFs and emails, runs recipes that make content packs, and shows the results for review. Use when someone wants actual posts, ads, banners, emails or videos made from their autor templates, a batch of versions from a list or spreadsheet, downloads of rendered files, or to review, approve or flag rendered files.
license: MIT
compatibility: Requires the autor MCP server at https://autor.ai/mcp.
---

# Producing content with autor

**Content** is one set of field values for a published template: one product, one headline variant, one language. **Rendering** turns content into files: one image per frame, or a video, PDF or email. A **recipe** renders several templates together into a **content pack**.

The tools named here are autor's MCP tools; your host may show them with a prefix.

## 1. Pick the templates

1. `get_context()`, then `search({type:"template", projectId})`.
2. Read each template's fields and frames with `get({type:"template", id})`. Only published templates render. To finish or publish a draft, design it first.

## 2. Write the content

- `save_content({items:[{templateId, name, values:{headline:"…", photo:"/assets/<id>"}}]})` saves up to 50 items per call, one per version. Keys are field ids.
- Write copy in the brand's voice, read from `get({type:"brand_kit", id: projectId})`. Fit the space the design gives: a five-word headline slot is not for twenty words.
- Image values are `/assets/<id>` embed URLs. Upload new images first with `upload_asset`, sending every file in one `files` list. Add `#autor-position=x,y` (0 to 100) to set the crop.
- Choice fields take one of their options. Markup fields accept `*italic*` and `**bold**`.
- Building from the person's data (a pasted list or a spreadsheet)? Show how you'll map columns to fields and confirm it before saving many items.
- The result is per item. Fix the failed items and don't resend the ones that succeeded.

## 3. Render and check

- `render({templateId, contentIds})` renders each content item. Narrow it with `frameIds`, or set `type` and the `pdf` or video options.
- Identical inputs reuse existing outputs, so rendering again after a partial failure is safe and cheap.
- Long work (videos, many items): pass `background:true` and poll `get({type:"operation", id, waitMs:20000})`. On an uncertain response, retry with the same `idempotencyKey`.
- Look before you present: `preview({kind:"output", id})`, with `page` for a PDF page, `timestamp` for a video frame, or `timestamps` for several frames on one strip.

## Recipes and content packs

- A recipe is an ordered set of published templates made together, such as a launch kit with a feed post, a story and an email. Create it with `save_recipe({projectId, name, templateIds})`.
- `run({recipeId, payload})` makes a content pack from field values. `retryRunId` retries only the failed items.
- Change one file in a pack with `save_content({items:[{runId, itemId, values}]})`, which re-renders just that file.

## Show the results

- When your host offers `show`, call it with the outputs or the run so the person sees the files inline.
- Otherwise share the review links the tools return and describe what was made.
- For a file to hand on, `get({type:"output", id, include:["download"]})` returns a short-lived download URL.

## Review belongs to the person

- `review_creation({kind:"pack", id})` reads each file's decision and open flags, plus a `snapshotHash`.
- Approve, reject or comment only when the person asks:
  - `review_creation({kind, id, decision:"approved", snapshotHash})` approves every finished file. For a workflow run this also sends its review deliveries; `deliveries` says what was sent and why anything is still held.
  - `decision:"rejected"` with a `reason` flags them all.
  - `comment` flags one file.
- `itemIds` returns those files' current values; `edit: {itemId, outputId, values}` changes one file and renders it again.
- `download: true` adds a link to the finished files: the file, or a ZIP of several.
- Approving never publishes or sends anything.
- Delete content or outputs only when asked.

`get_guide({topic:"inspection"})` and `get_guide({topic:"operations"})` have the exact arguments. When a tool fails and its recovery hint doesn't help, call `send_feedback` with the tool and `requestId`.
