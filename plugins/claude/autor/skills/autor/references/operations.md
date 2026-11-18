# Long work, retries and checks

- `import_brand`, `render`, `run` and `preview` can take a while. Pass `background:true` (`import_brand` defaults to it) to get an operation back at once.
- Poll with `get({type:"operation", id, waitMs:20000})`. `queued`, `running` and `waiting` are unfinished; `completed` has the result; `failed` has a known error.
- `uncertain` means the outcome is unknown. Look at the affected items before starting new work, and never replay it with a new key. If its saved result turns up, the operation reports `waiting`, `completed` or `failed` instead.
- After an uncertain response, retry with the same `idempotencyKey` and arguments to recover the same work. A different payload with that key is refused.
- After reconnecting, `search({type:"operation"})` lists your unfinished work.
- `dryRun:true` on a write checks arguments and preconditions without changing anything.
- Writes return what changed plus the `revision` to send back as `expectedRevision` on your next write.
