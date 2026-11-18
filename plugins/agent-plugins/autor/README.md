# autor

autor turns your brand kit and HTML templates into on-brand images, videos,
PDFs and emails. This plugin connects your assistant to autor and teaches it
how to work there: import a brand from its website, explore directions with
you on a shared sketch, build templates with editable fields, and render
finished files for you to review in autor.

You need an autor account. Sign in when your assistant first connects; autor
never asks for passwords in chat, and nothing is published or sent without
your approval in autor.

## Install

**Claude Code**

```
/plugin marketplace add autor-ai/plugins
/plugin install autor@autor
```

Then run `/mcp` and sign in to autor.

**Claude (web, desktop, Cowork)**: Customize → Plugins → Add marketplace →
`autor-ai/plugins`, install **autor**, then connect autor on the plugin's
Connectors tab.

**ChatGPT**: find **autor** in the Plugins directory and connect your account.

**Codex**

```
codex plugin marketplace add autor-ai/plugins
codex plugin add autor@autor
```

**Cursor and Antigravity**: add the marketplace `autor-ai/plugins` and install
**autor** (the package is in `plugins/agent-plugins/autor`).

**Any other agent**: add autor's MCP server, `https://autor.ai/mcp`, and the
skills with `npx skills add https://autor.ai`. autor's MCP server also serves
the skills itself, so a connected agent can read them with `get_guide`.

## Skills

Start with `autor`: it explains how autor works and which skill fits the job.

| Skill | What it does |
| --- | --- |
| `autor` | How projects, brand kits, sketches, templates and renders fit together |
| `setting-up-a-brand` | Imports a brand kit from a website and fixes logos, colors, fonts and voice |
| `exploring-on-sketches` | Explores directions with you on a shared canvas |
| `designing-templates` | Builds, previews, reviews and publishes templates, with a design craft guide |
| `designing-video-templates` | Builds video templates with motion, audio and footage that renders |
| `adapting-to-formats` | Adds story, feed, banner and email sizes to one design |
| `checking-brand-compliance` | Reviews designs and files against the brand kit |
| `producing-content` | Fills templates, renders files and shows the results |
| `making-batches-from-data` | Turns a list or spreadsheet into many finished files |
| `automating-workflows` | Turns app events and data into delivered files (not in ChatGPT) |

## Support

Questions and bugs: https://autor.ai/support. Privacy: https://autor.ai/privacy-policy.

This repository is generated from autor's source on every release, so pull
requests here can't be merged. Send suggestions to https://autor.ai/support.
