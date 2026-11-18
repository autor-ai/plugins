# Sketch panels

A sketch panel lets people search or browse one of the organization's apps from a sketch and bring what they find onto the canvas. Write one when someone wants an app's content on their sketches. It belongs to the app (a Composio toolkit, or a custom connector) and shows on every sketch in projects connected to it. It only reads.

autor turns panels on per organization. If saving answers `feature_not_in_plan`, tell the person to ask autor to enable sketch panels.

## Save one

`connect_account({projectId, name, provider, toolkit, panel:{definition, tools, code?}})`; pass `connectionId` to reuse a connection and `panel.panelId` to save the next version. Read the app's current panels first with `find_capability({kind:"toolkits", toolkit})`.

`tools` pins each tool the panel may read: `{SLUG: version}`. Composio tools need an explicit toolkit version and the `readOnlyHint` tag (`find_capability({slug})` shows both); a custom connector's tools take its version number. Try the tools with `try_capability` first.

## definition

`{title, description?, spec}`. `spec` is `{root, elements:{id:{type, props, children?, when?, unless?}}, state?, load?}`: a tree of elements from autor's catalog, which autor draws in its own style. No HTML, CSS or scripts.

- Values can read state, `{$state:"/results/items"}`, or inside a row, that row: `{$item:"title"}`.
- `when`/`unless` show an element only while a value is truthy/falsy.

## Catalog

| Element | Props |
| --- | --- |
| Stack | `direction?: "column" \| "row"`, `gap?: "sm" \| "md" \| "lg"`, `align?`; holds children |
| Text | `text`, `tone?: "muted"`, `size?: "sm"` |
| Heading | `text` |
| TextField | `bind:"/query"`, `placeholder?`, `label?`, `submit?: action`, `autoFocus?`, `multiline?` |
| Select | `bind`, `options: [{value,label}]` or `{$state}`, `label?`, `change?: action` |
| Button | `label`, `action`, `variant?: "primary" \| "secondary" \| "ghost"`, `disabled?` |
| ImageGrid | `items`, `image:"field"`, `label?:"field"`, `add: action`, `columns?: 2 \| 3`, `empty?`; people pick pictures and add them |
| List | `items`, `title:"field"`, `subtitle?`, `icon?` (emoji or https URL), `open?: action`, `add?: action`, `addLabel?`, `empty?` |
| Notice | `text`, `tone?: "info" \| "error"` |
| SelectionList | `empty?` |

## Actions

One action, or a list run in turn:

- `{action:"call", tool, args?, into?:"/path", append?, prepend?, then?:"add"}` reads a tool.
- `{action:"run", fn, args?, into?, append?, prepend?, then?}` runs one of the panel's functions.
- `{action:"set", path, value}` and `{action:"add", items}`.

`into` puts the result in state; `append` adds a next page's lists after the last, `prepend` puts new results first; `then:"add"` adds the result's items to the canvas. `spec.load` runs when the panel opens.

## Canvas items

`{kind:"image"|"video", url, label?}` (autor downloads and stores it; no SVG), `{kind:"note", text, title?}`, `{kind:"link", url, title?}`. At most 24 per add. They land beside the selected item, or in view.

## Working on what's selected

Set `spec.selection` `{kinds:["image"], min?, max?}`. Then `{$selection:"images"}` (or `"image"` for the first) in a tool's args becomes short-lived https links to the selected pictures, made by autor when the action runs; `{$selection:"count"}` is how many are selected (use it in `when`/`unless`). SelectionList shows the selection, or what to select. Results added with `then:"add"` land beside the originals. `spec.menu` `[{label, action}]` (up to 6) puts quick actions such as "Remove background" on the right-click menu of items that fit the selection.

## code

Optional, 64 KiB: one JavaScript module exporting async functions that shape tool results. Its `call` takes a tool slug and arguments, reads only the pinned tools on the connection in use and returns the tool's data. No network, imports or secrets. Limits per run: 20 calls, 1s CPU, 60s, 1 MiB result.

```js
export async function search({ query = "" }, { call }) {
  const data = await call("NOTION_SEARCH_NOTION_PAGE", { query });
  return { items: data.results.map((page) => ({ id: page.id, title: page.url })) };
}
```
