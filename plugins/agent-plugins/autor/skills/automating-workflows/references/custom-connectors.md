# Custom connectors

Use a custom connector when no app in `find_capability` covers an API. It belongs to the organization; its code runs sandboxed in autor and only reads data, as workflow sources and `try_capability` reads.

## Save one

`connect_account({projectId, name, provider:"custom", connector:{definition, code}})` saves version 1 and returns the autor link where a person enters the credentials. Pass `connector.connectorId` to save the next version. Versions never change, and workflows pin one.

Read the current definition and code with `find_capability({kind:"toolkits", toolkit: connectorId})` before saving a new version.

## definition

```js
{
  name, description?,
  credentials: [{name:"apiKey", label:"API key", help?}],
  inject: [{in:"header", name:"X-Api-Key", value:"{apiKey}"}],   // or {in:"query", name, value} or {in:"basic", username, password}
  endpoints: [{origin:"https://api.example.com", pathPrefix:"/v1/", methods:["GET"]}],
  tools: [{slug:"LIST_ITEMS", name, description, input: <JSON schema>, output?: <JSON schema>}],
  test?: {tool:"LIST_ITEMS", arguments:{}}
}
```

## code

One JavaScript module exporting an async function per tool slug. Return JSON. No imports, packages or secrets.

```js
export async function LIST_ITEMS({ page = 1 }) {
  const r = await fetch("https://api.example.com/v1/items?page=" + page);
  if (!r.ok) throw new Error("HTTP " + r.status);
  return { records: (await r.json()).items };
}
```

`fetch` reaches only the declared endpoints: GET, HEAD, or POST for APIs that take a query in the body, such as search or GraphQL (1 MiB body). autor adds the credentials per `inject`, so code never sees them. Limits per call: 100 requests, 2s CPU, 60s, 8 MiB per response, 4 MiB result.

## Credentials and first read

Never ask for credentials in chat; people enter them in autor, where the test read checks them. A version runs in saved workflows once one read through it has worked: try it with `try_capability({connectionId, toolSlug, toolkitVersion:"<version>", arguments})`.

## In a workflow

Read it as an app source: `{alias, type:"app", connectionId, tool:{slug, version:"<version>"}, arguments, recordsPath:"records"}`.
