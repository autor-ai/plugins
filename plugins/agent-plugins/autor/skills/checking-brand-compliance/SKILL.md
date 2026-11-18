---
name: checking-brand-compliance
description: Checks autor templates, content and rendered files against the project's brand kit (logos, colors, fonts, design guidance and tone of voice) and records the findings as a design review. Use when someone asks whether a design or file is on brand, wants a brand or tone review before publishing in autor, or brand enforcement blocks publishing. Load it before the first record_design_review call.
license: MIT
compatibility: Requires the autor MCP server at https://autor.ai/mcp.
---

# Checking brand compliance in autor

A design review compares one thing (a draft template, a published template, a content item or a rendered file) with the brand kit, and saves what you found. With brand enforcement on, a template can't be published without a current review.

The tools named here are autor's MCP tools; your host may show them with a prefix.

## 1. Gather the evidence

1. `get({type:"review_context", id, subjectType:"template_draft"})` returns the brand rules, voice, design guidance and the `revision` your review must name. `subjectType` is `template_draft`, `template`, `content` or `output`.
2. Look at the thing itself, never only its source:
   - a template: `preview({kind:"template", id, frameIds:["all"]})`;
   - a rendered file: `preview({kind:"output", id})`, with `page` or `timestamps` for PDFs and video.
3. Read the brand kit's tokens when you need exact values: `get({type:"brand_kit", id: projectId})`.

## 2. Check, in this order

- [ ] **Logo**: the kit's own file, not redrawn or recolored, with clear space, on a background it was made for, at a readable size.
- [ ] **Colors**: every color in the source is a kit token, or a tint the guidance allows. List any hex value that isn't.
- [ ] **Type**: only the kit's families, with their font files loaded. A fallback font in the preview is a finding.
- [ ] **Explicit rules**: anything the design guidance says must or must not happen (for example "never put the logo on a photo").
- [ ] **Voice**: headlines and copy against the tone-of-voice guidance: wording, formality, length, punctuation, emoji.
- [ ] **Craft**: overflow, clipping, contrast below 4.5:1, text in platform safe zones (see `get_guide({topic:"designing-templates", file:"references/craft.md"})`).

Be honest about gaps: if the kit has no hex values, fonts or guidance for something, say you couldn't check it. Never invent a rule or a brand color to check against.

## 3. Record the review

`record_design_review({subjectType, subjectId, expectedRevision, findings})`, with `expectedRevision` from the review context.

- Each finding is `{severity, message, area}`. Say what's wrong, where, and the fix: "Headline uses #6B4EFF; the kit's accent is #5B3FE8."
- `severity` is `blocking` only when the finding breaks an explicit brand rule. Off-palette colors and wrong fonts are usually `warning`; taste is a `suggestion`.
- Use area `Tone of voice` for voice findings.
- Empty `findings` means compliant. Don't pad a review with suggestions to look thorough.

## 4. Fix, or hand back

- For a template you're allowed to change, fix the findings with one `edit_template` call, preview again, and record a new review.
- For rendered files, flag one with `review_creation({kind:"output", id, comment})` only when the person asks you to; otherwise list the findings in your reply.
- Blocking findings stop publishing under enforcement. Only an admin's `overrideReason` gets past them: never send one unless that admin tells you to.

When a tool fails and its recovery hint doesn't help, call `send_feedback` with the tool and the IDs involved, not the person's content.
