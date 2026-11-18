# Sketch items and edits

Every item kind and edit `edit_sketch` takes. Each section stands alone.

## Contents

- Placement
- Item kinds
- Assets in items
- Building in view
- Long pages and changes
- Activity
- Reading what people did
- Seeing the canvas

## Placement

Each item takes `x`, `y` (canvas pixels, y grows down) and `w`, its width on the canvas; the height follows. Items without `x`/`y` flow into rows below. Renders, images and links default to 480px on their long side, notes to 480px wide. Label everything you add: a label shows only when someone selects the item.

## Item kinds

| Kind | Takes | Use for |
| --- | --- | --- |
| `render` | `{html, width, height}` at its real size (such as 1080×1920), `{outputId}`, or `{templateId, frameId?, contentId?}` | A design |
| `image` | `{assetId}` or `{sourceUrl}` | A photo or picture; it always keeps its own shape |
| `link` | `{url, title?}`, plus `assetId` or `sourceUrl` only for a screenshot of your own | A web page; without one it shows the page's share image and icon once someone opens the sketch |
| `note` | `{text, title?}` | Plain text, only when the person asks for it |
| `palette` | `{colors:[{value:"#7A2E43", name:"Berry"}]}` | Colors |
| `type` | `{text, fontFamily, fontUrl?, fontWeight?}` | A type specimen |
| `group` | `{label, x, y, width, height}` | A dashed region around related items |
| `html` | `{html, width, height}` | Anything else: rankings, crop studies, charts, clickable prototypes |

People can scroll and click `render` and `html` items, and play videos, in place.

## Assets in items

HTML uses `/assets/<id>` embed URLs for images and fonts, as templates do. Upload local images with `upload_asset` first; `image` and `link` items take a `sourceUrl` directly. `upload_asset` returns an image's width and height, and items in `get({type:"sketch", id})` carry their real width and height, so layouts can keep each image's shape.

## Building in view

People watch the canvas fill in as your cursor goes to each change. Add a few items per call (1–4), passing the last reply's revision as `expectedRevision`. If people changed other items meanwhile, your edit merges; it conflicts only on items both of you changed. A contact sheet of ready images can still go in one call.

If your host asks the person to confirm every tool call, send a round in one `edit_sketch` call instead.

## Long pages and changes

- Long HTML shows as it's written: add the item with its frame and first section, then send each further section with `update:[{id, appendHtml}]`, one section (about 1–4 KB) per call. If your host confirms every call, send the whole page at once.
- Change a page with `update:[{id, edits:[{search, replace}]}]`. Each `search` matches once; read the page first with `get({type:"sketch", id, include:["html"]})`. Never send all of it again for a small change.
- For a variation, `add:[{copy: itemId, x, y}]`, then edit the copy.

## Activity

- Before slow work (long HTML, renders, uploads), send `activity:{status:"working", message:"Rendering direction 2 in green", placeholder:{x, y, width, height}}`. People see your cursor on a ghost there, and the next item you add without `x`/`y` lands on it.
- Writes without activity show as working for a few minutes.
- When a round is ready, send `activity:{status:"input_required", message:"Pick a direction"}`.

## Reading what people did

`get({type:"sketch", id})` returns:

- `pickedBy` on items people starred;
- open comments, each with its `itemId` and `pin` (0–1 of the item), or `at` (canvas x/y);
- `movedByPerson` on items people moved, and `agent: null` on items people dropped or pasted in themselves.

Answer comments with `reply:[{id, body}]` and close what you addressed with `resolveComments:[{id, note}]`.

Items a person moved or brought in stay where they put them: your `x`/`y`/`w` for them is ignored and listed in `kept`. Change their content freely, but read their HTML again before editing it: people may have edited it.

## Seeing the canvas

`preview({kind:"sketch", id})` returns one image with numbered items (★ marks picks) and a legend of item ids. `itemIds:[…]` shows just those, side by side.

Only people archive a sketch. An archived sketch takes no edits.
