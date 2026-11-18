# Previews and downloads

How to look at templates, sketches and rendered files. Look before you show anything to the person.

- `preview({kind:"template", id})` returns an image and a revision fingerprint. Add `frameId`, `contentId` or `versionId` to narrow it.
- Every frame at once: `preview({kind:"template", id, frameIds:["all"]})` puts them on one numbered sheet, and `tiles` maps numbers to frame ids. One call per template, not one per frame.
- A rendered file: `preview({kind:"output", id})`. `page:2` shows a PDF page, `timestamp` (seconds) shows a video frame, and `timestamps:[0,2,4,6]` shows several on one strip, for video templates and outputs alike.
- A sketch: `preview({kind:"sketch", id})` returns one image with numbered items and a legend.
- Downloads: `get({type:"output", id, include:["download"]})` returns a short-lived URL. Fetch files outside the conversation when you can.
- When your host offers `show`, call it with templates, outputs or a sketch so the person sees them inline.
