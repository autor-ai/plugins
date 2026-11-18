---
name: designing-video-templates
description: Builds autor video templates, either as a Remotion composition with editable fields passed in as props, or by turning an image template's frames into a sequence, and previews them at chosen timestamps. Covers motion, timing, audio and footage that renders reliably. Use when someone wants a video, reel, animated story, motion version of a design, narrated film or slideshow template in autor. Load it before creating or editing a video template.
license: MIT
compatibility: Requires the autor MCP server at https://autor.ai/mcp.
---

# Designing video templates in autor

Two ways to make a video template:

- **From an image template**: `create_template({from:{templateId, as:"video", frameDurationInFrames:90, transition:"fade"}})` plays the template's frames one after another. It's the quickest route to a slideshow or an animated story, and it keeps the image template's fields.
- **A Remotion composition**: `create_template({projectId, name, type:"video"})` starts a React project rendered by Remotion. Use it for real motion, timing and audio.

The tools named here are autor's MCP tools; your host may show them with a prefix. Everything in `designing-templates` about the brand kit, assets, editing and publishing applies here too.

## The Remotion project

The starter has `src/index.tsx` (calls `registerRoot`), `src/Root.tsx` (one `<Composition id="studio" …>` with `durationInFrames`, `fps`, `width`, `height`) and `src/Composition.tsx`. Keep the composition id the template's manifest names.

- **Fields are props.** Add them with `edit_template({templateId, expectedRevision, addFields:[{id:"headline", label:"Headline", type:"text", defaultValue:"…"}]})`; no selector is needed. Each content item's values reach the composition as props keyed by field id (`props.headline`), and `defaultProps` should match the defaults.
- **Lists drive length.** An array field arrives as a real array. Derive sequences and the duration from its length with `calculateMetadata`, so a five-item list makes a longer video than a two-item one.
- **Text fields arrive raw**: markup formatting is for HTML templates only.

## Assets and media

- Images, fonts, audio and video go in as `/assets/<id>` embed URLs, as everywhere in autor: `<Img src="/assets/<id>" />`, `<Audio src="/assets/<id>" />`, `<OffthreadVideo src="/assets/<id>" />`.
- Prefer `OffthreadVideo` for footage.
- Phone footage often fails to render: iPhone clips are 4K HEVC 10-bit HDR, which the renderer can't decode or runs out of memory on. Ask for, or convert to, H.264 1080p 8-bit SDR before uploading.
- The preview plays at most 24 audio elements at once; reuse one `<Audio>` per track instead of one per scene.

## Motion that looks designed

- Animate with `interpolate` and `spring` from `useCurrentFrame()`, never with CSS transitions or timers: renders are frame by frame.
- Give each scene one entrance and one exit. Ease out on entrances (`Easing.out(Easing.cubic)`), ease in on exits; springs with high damping (around 200) for UI-like moves, lower for playful ones.
- Hold readable text long enough: about 1 second per 3 words, plus half a second.
- Keep the brand's type scale and colors from the kit, and keep text in the platform's safe zones (for 9:16, clear of the top ~250px and bottom ~340px).
- Captions on: most social video plays muted. Put spoken lines on screen as text.

## Preview, then publish

1. `preview({kind:"template", id, timestamps:[0,2,4]})` shows several moments on one strip. Make one preview call at a time with 2–4 timestamps: bigger strips or parallel calls can drop.
2. Check entrances (frame 0 shouldn't be blank unless intended), the busiest moment and the last frame.
3. `publish_template({templateId, expectedRevision})`. Editing a published template makes a draft again; publish before rendering.
4. Render with `render({templateId, contentIds, background:true})` and poll `get({type:"operation", id, waitMs:20000})`: a minute of video can take a few minutes. Check the result with `preview({kind:"output", id, timestamps:[…]})`.

When a tool fails and its recovery hint doesn't help, call `send_feedback` with the tool and the IDs involved, not the person's content.
