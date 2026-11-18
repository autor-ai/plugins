---
name: autor
description: Start here for any work in autor, the brand design tool that turns a brand kit and HTML templates into on-brand images, videos, PDFs and emails. Explains how projects, brand kits, sketches, templates, content and renders fit together, and which autor skill to load for each job. Use whenever someone mentions autor, connects it, or asks for on-brand designs, posts, ads, banners, emails or videos made with it.
license: MIT
compatibility: Requires the autor MCP server at https://autor.ai/mcp.
---

# autor

autor makes on-brand design files from a project's brand kit and HTML templates. You do the making through autor's MCP tools; people review, pick, approve and deliver in autor. The tools named here are autor's MCP tools; your host may show them with a prefix.

## How it fits together

1. A **project** is one brand, client or product line. Everything below belongs to one.
2. Its **brand kit** (logos, colors, fonts, design guidance, voice) is the source of truth for everything made in it.
3. A **sketch** is a shared canvas where you explore options with the person, round by round, before anything becomes a template.
4. A **template** is HTML and CSS (or a Remotion composition for video) with editable **fields** and **frames** (sizes). Only published templates render.
5. **Content** is one set of field values for a template. **Rendering** turns content into files. A **recipe** renders several templates together into a **content pack**.

## Start every task

1. `get_context()` returns the organization, the working project, recent projects and unfinished operations. Reuse its project id for the whole task, or switch with `get_context({projectId})`.
2. Read the brand kit before you make anything: `get({type:"brand_kit", id: projectId})`.
3. Load the skill for the job, below. Trust the live tool list over any skill text: if a tool or argument a skill names isn't there, your host or plan doesn't offer it.

## Which skill

| The person wants to… | Skill |
| --- | --- |
| Start with autor, add a brand, or fix a wrong logo, color, font or voice | `setting-up-a-brand` |
| See options, directions, variations, a moodboard or a comparison | `exploring-on-sketches` |
| Build, change, import or fix a template, or address comments on one | `designing-templates` |
| Make a video template, or motion from a still design | `designing-video-templates` |
| Make one design work in more sizes (story, feed, banner, email) | `adapting-to-formats` |
| Check a design or file against the brand | `checking-brand-compliance` |
| Get finished files from published templates | `producing-content` |
| Make many versions from a list or spreadsheet | `making-batches-from-data` |
| Automate content from events, schedules or connected apps | autor's automation skill, where your host has autor's automation tools |

Your host may already have these skills installed. If it doesn't, read one with `get_guide({topic:"designing-templates"})`, and a file it lists with `get_guide({topic:"designing-templates", file:"references/craft.md"})`.

Design quality matters in every job that draws something: read `get_guide({topic:"designing-templates", file:"references/craft.md"})` before the first design of a session.

## Ground rules

- **The brand kit wins.** Use its colors, fonts, logo files and voice. Never invent a brand color or font; when the kit is missing something, say so and ask.
- **People stay in charge.** Approvals, account connections and delivery happen in autor: share the links tools return. Approve, reject, archive or delete only when the person asks. Never ask for passwords, API keys or tokens in chat.
- **Look before you show.** Preview what you made, fix overflow, clipping and contrast, then show it (with `show` when your host offers it).
- **Batch your writes.** Some hosts ask the person to confirm every tool call, so put one request's changes in one call where a tool takes several.
- **Revisions.** Writes return a `revision`; send it back as `expectedRevision` on your next write. On `revision_conflict`, read again and reapply your change.

Long-running work, retries and dry runs are in [references/operations.md](references/operations.md); previews and downloads in [references/previews.md](references/previews.md). Without those files, read them with `get_guide({topic:"autor", file:"references/operations.md"})`.

When a tool fails and its recovery hint doesn't help, or a capability is missing, call `send_feedback` with the tool and the IDs involved, not the person's content.
