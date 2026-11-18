---
name: making-batches-from-data
description: Makes many versions of autor templates from the person's data, such as a pasted list, a CSV or spreadsheet, a product feed or a set of photos, by mapping columns to template fields, saving content items in batches and rendering them. Use when someone wants dozens of posts, ads, banners or emails from a list or a spreadsheet in autor, one per product, location, date or language. Load it before saving more than a few content items with save_content.
license: MIT
compatibility: Requires the autor MCP server at https://autor.ai/mcp.
---

# Making batches from data in autor

One row of the person's data becomes one **content item**: one set of field values for a published template. A batch is many content items rendered together.

The tools named here are autor's MCP tools; your host may show them with a prefix.

## 1. Read the template and the data

1. Find the published template: `search({type:"template", projectId})`, then `get({type:"template", id})` for its fields (ids, types, options) and frames.
2. Read the data the person gave you: a pasted table, a CSV or spreadsheet file, a list. If it comes from an app they use, ask them to export it or paste it here.
3. Count the rows. Over about 200, agree on a first slice before doing all of it.

## 2. Map columns to fields, and confirm

Show the person a short mapping before saving anything:

| Field | Column | Notes |
| --- | --- | --- |
| `headline` (text) | Product name | |
| `price` (number) | Price | Strip the currency sign |
| `photo` (image) | Image URL | Uploaded to autor first |
| `layout` (choice) | none | `left` for every item |

- Fill a field from a column, a fixed value, or copy you write from the row (say which).
- Flag rows that won't fit: text longer than the design allows, missing images, values outside a choice field's options.
- Ask before writing copy for many rows: they may want their own wording.

## 3. Prepare images

Image values are `/assets/<id>` embed URLs. Upload every image first with one `upload_asset` call and the whole `files` list (public URLs as `sourceUrl`; local files get signed upload URLs). Add `#autor-position=x,y` (0 to 100) to an image value to set its crop when the subject isn't centered.

## 4. Save content in batches

`save_content({items:[{templateId, name, values:{headline:"…", price:12.5, photo:"/assets/<id>"}}]})` saves up to 50 items per call.

- Give each item a `name` the person will recognise: the row's product, place or date.
- Keys are field ids, not labels. Array fields take real arrays.
- The result is per item. Fix the failed items and resend only those.
- Keep a list of `contentId`s by row so you can report and retry.

## 5. Check a sample, then render

1. Preview two or three of the hardest rows first (the longest text, the oddest image): `preview({kind:"template", id, contentId})`. Fix the template or the values before rendering everything.
2. `render({templateId, contentIds, background:true})` for the batch, narrowing with `frameIds` if only some sizes are needed. Poll `get({type:"operation", id, waitMs:20000})`.
3. Identical inputs reuse existing outputs, so rendering again after a partial failure is safe and cheap.

To make several templates per row (a feed post, a story and an email for each product), save a recipe with `save_recipe({projectId, name, templateIds})` and call `run({recipeId, payload})` with an array of rows; each row becomes one content pack.

## 6. Report back

Tell the person how many items rendered, which rows failed and why, and where to review them: share the review links the tools return, or call `show` when your host offers it. Approving and delivering happen in autor, when the person decides.

When a tool fails and its recovery hint doesn't help, call `send_feedback` with the tool and the IDs involved, not the person's data.
