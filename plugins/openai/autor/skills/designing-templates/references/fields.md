# Fields and frames

Reference for `edit_template` field and frame changes. Each section stands alone.

## Contents

- Field types
- Fixed choices
- Rich text
- Image crops
- Links
- Lists
- Frames
- The template manifest

## Field types

`addFields` takes `{label, type, selector}` plus optional `options`, `format`, `frameId`, `defaultValue` and `required`. Types:

| Type | Use for | Content value |
| --- | --- | --- |
| `text` | Headlines, body, labels | A string |
| `image` | Photos, product shots, logos that vary | An `/assets/<id>` embed URL |
| `number` | Prices, counts | A number |
| `link` | Button and link destinations | A URL |
| `enum` | Layout, theme, badge choices | One of `options` |
| `array` | Repeating items (jobs, products, agenda) | A list of objects |

One field can feed several elements: a selector such as `.series` binds every match in every frame, so a title repeated across the story and the feed post is one value.

Change a field with `fieldUpdates:[{id, ...changed}]` and remove one with `removeFieldIds`.

## Fixed choices

`{label:"Layout", type:"enum", selector:".card", options:["left","right"]}`, or `options:[{value, label}]` for friendlier names. The bound element gets `data-autor-value="<chosen value>"`. Style each variant in CSS:

```css
[data-autor-field="layout"][data-autor-value="right"] .photo { order: 2; }
body:has([data-autor-value="dark"]) { background: var(--ink); }
```

## Rich text

A text field with `format:"markup"` (or `data-autor-format="markup"` in imported HTML) renders `*italic*`, `**bold**`, and `|` or a newline as a line break. Everything else is escaped. HTML templates only.

## Image crops

An image value ending in `#autor-position=x,y` (0 to 100) sets where the image sits, for example `/assets/<id>#autor-position=50,20`. It becomes `object-position`, or `background-position` on a background image, so a template needs no separate crop control.

## Links

Bind a link's destination separately from its label:

```html
<a data-autor-link="applyUrl" href="https://example.com/jobs">
  <span data-autor-field="applyLabel">Apply now</span>
</a>
```

`detectLinks:true` binds the links already in the source.

## Lists

An `array` field repeats an item template inside its container, up to 100 items, without nesting. Put at least one item in the container and tag its parts:

```html
<ul class="jobs">
  <li><h3 data-autor-field="title">Designer</h3> <span data-autor-field="city">Lisbon</span></li>
  <li><h3 data-autor-field="title">Engineer</h3> <span data-autor-field="city">Berlin</span></li>
</ul>
```

```json
{"id": "jobs", "label": "Jobs", "type": "array", "selector": ".jobs"}
```

The container's first child becomes `itemTemplate`, its tagged parts become `itemFields`, and the items there now become the default list. To set them yourself, send `itemTemplate` (one item's HTML) and `itemFields` (`[{id, label, type}]`, each bound with `data-autor-field` inside the template). Values are real arrays: `{"jobs": [{"title": "Designer", "city": "Lisbon"}]}` in `save_content` values, `defaultValue`, or a run payload.

`data-autor-index="2"` in item markup shows a two-digit position (`01`, `02`). For email, use table containers.

A `data-autor-frame` inside the item repeats once per item, in place between the fixed frames. Frame `session` renders as `session-1` … `session-N`, and render, preview `frameIds`, packs and review use those ids. So a cover, one poster per item and a closing frame are a single template. `get({type:"template", id})` marks such a frame `repeatsWith` the array field's id.

## Frames

Each size is an element marked `data-autor-frame="<id>"`, with an optional `data-autor-frame-name`. `frames:[{id, name, width, height}]` replaces the whole ordered list; frames you leave out are removed. Marked frames are kept as they are on import.

## The template manifest

`autor.template.json` is a patch: keys you leave out keep their values, fields merge by id (an id-less field matches by label), and fields stay while the source still binds them. Delete fields with `removeFieldIds`, not by leaving them out.
