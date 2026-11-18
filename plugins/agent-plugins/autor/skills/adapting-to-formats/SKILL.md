---
name: adapting-to-formats
description: Adapts one autor design to more sizes and placements (Instagram and Facebook stories, feed posts, reels covers, LinkedIn and X images, web banners, email headers) as frames of the same template, so one set of content renders every format. Use when someone wants a design resized, in more formats, for another platform, or a full set of sizes from one template in autor. Load it before adding frames with edit_template.
license: MIT
compatibility: Requires the autor MCP server at https://autor.ai/mcp.
---

# Adapting a design to more formats in autor

In autor, sizes are **frames** of one template, not separate templates. Every frame shares the template's fields, so one content item renders the story, the feed post and the banner with the same headline and photo. Adapt by adding frames, not by copying the template.

The tools named here are autor's MCP tools; your host may show them with a prefix.

## 1. Agree on the set

Ask which placements the person needs if they didn't say. Common sizes:

| Placement | Size |
| --- | --- |
| Instagram and Facebook story, reel cover, TikTok | 1080×1920 |
| Instagram feed portrait | 1080×1350 |
| Square feed post | 1080×1080 |
| LinkedIn and X image, link preview | 1200×627 |
| Wide banner or YouTube thumbnail | 1920×1080 or 1280×720 |
| Email header | 600 wide (1200 for retina), any height |

Video and email templates have their own rules: see `designing-video-templates`, and keep email at 600px wide with real text.

## 2. Read the source design

1. `get({type:"template", id})` for its fields and frames, then the source with `get({type:"template", id, include:["files"], paths:["index.html"]})`.
2. Preview the existing frame: `preview({kind:"template", id})`. Note what the design is about: the one thing that must come first, the image's focal point, the logo's place.

## 3. Recompose, don't scale

A new aspect ratio needs a new layout, not the old one shrunk or stretched.

- **Tall (9:16)**: stack. Message in the upper middle, image filling the frame, the call to action above the bottom safe zone. Keep the top ~250px and bottom ~340px clear.
- **Portrait (4:5) and square**: the closest to most source designs. Check the center still reads when the grid crops it.
- **Wide (1.91:1 and 16:9)**: side by side. Text on one half, image on the other; fewer words, bigger type, since these show small.
- **Email header**: short, the message as live text in the template, not baked into the image.

Keep the hierarchy, colors, type scale (scaled to the frame) and logo treatment. Drop secondary copy before shrinking type below readable sizes. Use the craft guide's safe zones and checklist: `get_guide({topic:"designing-templates", file:"references/craft.md"})`.

## 4. Add the frames in one edit

Mark each size's element with `data-autor-frame="<id>"` and a `data-autor-frame-name`, and give the frame list in the same call:

`edit_template({templateId, expectedRevision, replacements:[…], frames:[{id:"feed", name:"Feed 4:5", width:1080, height:1350}, {id:"story", name:"Story", width:1080, height:1920}]})`

- `frames` replaces the whole ordered list: include the existing frames.
- Bind new elements to the existing fields: a field's selector binds every match in every frame, so give the story's headline the same class the feed's headline uses. Don't add a second headline field.
- Use frame-specific CSS (`[data-autor-frame="story"] h1 {…}`) for the layout differences.

## 5. Check every frame

`preview({kind:"template", id, frameIds:["all"]})` shows them on one sheet. Then try long copy: render a preview with the longest realistic headline (`contentId` of a long content item, or a temporary default) and check every frame survives it. Fix overflow before you show anything.

Publish with `publish_template({templateId, expectedRevision})` when the person is happy. Rendering a content item now gives one file per frame; narrow it with `frameIds` when only some sizes are needed.

When a tool fails and its recovery hint doesn't help, call `send_feedback` with the tool and the IDs involved, not the person's content.
