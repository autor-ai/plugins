---
name: automating-workflows
description: Sets up autor workflows that turn events or data (a schedule, a webhook, a new row or page in a connected app, a website change) into rendered files delivered to social channels or other apps. Use when someone wants to automate content in autor, connect an app such as Google Sheets, Notion, Airtable, Slack or Instagram, or change, preview, pause or activate an autor workflow. Load it before the first find_capability, connect_account or save_workflow call.
license: MIT
compatibility: Requires the autor MCP server at https://autor.ai/mcp with its workflow tools.
---

# Automating workflows in autor

A workflow has one **trigger** (run now, a schedule, a webhook, an app event or a website change). It reads **sources**, can **decide** which items continue, **maps** record data onto template fields, can **generate** copy, **renders** the pinned templates, and **delivers** files to **destinations** (social channels or app actions). You author it here. People preview, approve and run it in autor.

The tools named here are autor's MCP tools; your host may show them with a prefix.

## 1. Pin down the job

Before touching tools, agree on these with the person:

- What starts it?
- Where do records come from?
- Which published templates render them, and in what order?
- Which fields come from data and which are generated?
- Where do files go?
- Should deliveries wait for approval?

Ask only what you can't infer. The template must be published. If it isn't, design and publish it first.

## 2. Discover capabilities

- `find_capability({kind:"toolkits", search:"sheets"})`, then `find_capability({kind:"tools", toolkit, search})`, then read one exact schema with `find_capability({slug, version})`.
- Toolkit `social` is social publishing. Toolkit `autor` covers schedules, webhooks and website monitoring, none of which needs an account.
- No app covers the API? Write a custom connector: [references/custom-connectors.md](references/custom-connectors.md).
- Want an app's content on sketches? Write a sketch panel: [references/sketch-panels.md](references/sketch-panels.md).
- Without those files, read them with `get_guide({topic:"automating-workflows", file:"references/custom-connectors.md"})`.

## 3. Connect accounts

- `connect_account({projectId, name, provider:"composio", toolkit})` returns an autor link. The person authorizes the account there.
  - Use `provider:"zernio"` for the project's social channels.
  - Connecting accounts needs an admin.
- Check the connection with `search({type:"connection", projectId})`.
- Never ask for passwords, API keys or tokens in chat.

## 4. Try before saving

`try_capability({connectionId, toolSlug, toolkitVersion, arguments})` runs reads for real and simulates writes. Use it to see real record shapes before you write the mapping.

To let a workflow decide which items continue, ask the Jev decision model typed questions (yes/no, choice, score) about text and images: `try_capability({toolSlug:"DECIDE", arguments:{state, images, questions, rules}})` needs no connection. Run it on a few real items to pick thresholds, then save them as a decision step; `find_capability({slug:"DECIDE"})` shows the shape. Each call is metered to the organization.

## 5. Write and save the spec

1. Read the grammar with `get_guide({topic:"workflow_spec"})`.
2. Save the workflow with `save_workflow({name, templateIds, spec})`. `templateIds` lists the templates it renders, in order.
3. Revise it with `save_workflow({workflowId, expectedRevision, spec})` (the workflow's `revision.id`).
4. Pause or activate with `state`, and toggle single steps with `steps`.

## 6. Preview and hand over

1. `run({workflowId, preview:true})` renders sample records without delivering anything. Check the files with `preview`, then show them to the person.
2. `workflow_setup({action:"review", workflowId, expectedRevision, previewId})` shows the saved revision and lets the person keep editing, pause or activate it.
3. Activation and automatic delivery need an admin.
4. Ask only unresolved choices with `workflow_setup({action:"ask", message, requestedSchema})`, as a small flat form.
5. After a timeout, resume with `workflow_setup({action:"resume", setupSessionId})`.

## Ground rules

- Nothing is sent or published without the person's approval in autor.
- Prefer `mode:"draft"` and `approval:"review"` for social destinations until the person asks otherwise.
- Pick each platform's post type with `platformOptions`, e.g. `{instagram:{contentType:"story"}, facebook:{contentType:"story"}}`. Stories can't be drafts, so use `mode:"publish"` with `approval:"review"`. `find_capability({toolkit:"social", slug:"SOCIAL_STORY"})` lists every option. TikTok accounts need `platformOptions.tiktok`.
- Spec keys autor doesn't know are rejected, not dropped. Fix the named key; don't invent settings.
- When a tool fails and its recovery hint doesn't help, call `send_feedback` with the tool and `requestId`.
