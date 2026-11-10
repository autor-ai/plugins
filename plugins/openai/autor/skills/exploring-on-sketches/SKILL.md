---
name: exploring-on-sketches
description: Explores visual directions with a person on an autor sketch, a shared canvas of renders, images, notes, palettes and type specimens that they pick from and comment on round by round, before anything becomes a template. Use when someone wants options, variations, directions, a moodboard, a contact sheet, a photo shortlist or a side-by-side comparison in autor, or asks to continue a sketch.
license: MIT
compatibility: Requires the autor MCP server at https://autor.ai/mcp.
---

# Exploring on sketches in autor

A sketch answers one question with the person, such as "How should the winter story ads feel?". You place things on a free canvas. They pick favorites (★), comment, and move or resize items. When they have chosen, you turn the winner into a template. Sketches are the place for options, so never create templates just to compare ideas.

The tools named here are autor's MCP tools; your host may show them with a prefix.

## Start or continue

1. `get_context()`, then read the brand kit with `get({type:"brand_kit", id: projectId})`. Use its tokens, fonts and asset `embedUrl`s in everything you draw.
2. Look for a sketch already asking this question: `search({type:"sketch", projectId})`. Continue it for the next round. Start a new sketch only for a new question.
3. Start one named by its question, with the first round in the same call:
   `edit_sketch({projectId, name:"Winter push directions", question:"How should the winter story ads feel?", add:[…], activity:{status:"working", message:"Drafting three directions"}})`.
   It returns `sketchId`, the item ids, `expectedRevision` and a URL. Share the URL so the person can watch.

## Make a round

- Build the round in view, 1–4 items per `edit_sketch` call, each with a short label, passing `expectedRevision` from the last reply. People watch the canvas fill in as your cursor goes to each change. If your host asks the person to confirm every tool call, send the round in one call instead.
- Make the options genuinely different in layout, hierarchy, imagery or type, not one design in three colors.
- Let the work speak: add no notes, headings or explanations to the canvas unless the person asks for them. Name directions with labels and groups, and give your reasoning in your reply. Labels show only when someone selects an item.
- Item kinds:
  - `render`: a design at its real size (`html`, `width`, `height`, such as 1080×1920).
  - `image`: an `assetId` or `sourceUrl`. It always keeps its own shape; `upload_asset` and `get` report its real `width` and `height`.
  - `link`: a URL. It shows the page's share image, icon and title on its own once someone has the sketch open; pass `assetId` or `sourceUrl` only to show a screenshot of your own.
  - `note`: plain text on the canvas, when the person asks for it.
  - `palette`, `type`: colors and type specimens.
  - `group`: a dashed region around related items.
  - `html`: anything else, such as a ranking, a crop study or a chart.
- Placement: `x`, `y` in canvas pixels (y grows down) and `w`, the width on the canvas. Items without `x`/`y` flow into rows. Group each direction so the canvas reads at a glance.
- Write long HTML in view too: add the item with its frame and first section, then send each further section with `update:[{id, appendHtml}]`, one section (about 1–4 KB) per call, so people watch the page fill in.
- Change a page with `update:[{id, edits:[{search, replace}]}]`: each `search` must match exactly once in its html (read it with `get({type:"sketch", id, include:["html"]})`). Never send the whole page again for a small change. For a variation, `add:[{copy: itemId, x, y}]` and then edit the copy.
- Before slow work, such as long HTML, renders or uploads, send `activity:{status:"working", message, placeholder:{x, y, width, height}}` first. People see your cursor on a ghost there, and the next item you add without `x`/`y` lands on it. When the round is ready, set `activity:{status:"input_required", message:"Pick a direction"}`.
- Check the canvas yourself with `preview({kind:"sketch", id})`, which returns one image with numbered items and a legend.

## Read their response

`get({type:"sketch", id})` returns:

- `pickedBy` on the items people starred;
- open comments, each with its `itemId` and pin, or a canvas position;
- `movedByPerson` on items people moved, and `agent: null` on items people dropped or pasted in themselves.

Answer comments with `reply:[{id, body}]` and close what you addressed with `resolveComments:[{id, note}]`. Items people moved stay where they put them: your `x`/`y`/`w` for them is ignored and listed in `kept`, but you can still change their content.

## Next round and finish

- Build the next round from the picks: put variations next to the chosen direction instead of starting a fresh canvas.
- When they have chosen, `create_template({from:{sketchId, itemId}})` copies an `html` or `render` item into a draft template. Continue from there to fields, preview and publishing.
- Only people archive or delete a sketch. An archived sketch takes no more edits.

`get_guide({topic:"sketches"})` lists every item field. When a tool fails and its recovery hint doesn't help, call `send_feedback` with the tool and `requestId`.
