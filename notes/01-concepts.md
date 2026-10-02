# n8n concepts

Notes I kept while building the lead qualifier. Written from things I actually hit
rather than copied from the docs.

**Node** — one step in a workflow. It takes items in, does something, passes items out.
Most of the work is choosing the right node and getting the data shape right between them.

**Trigger vs regular node** — a trigger starts a workflow and has no input: a webhook
receiving a form post, a schedule, an error from another workflow. Everything after it is
a regular node. A workflow can only start from a trigger.

**Items, and why everything is an array** — nodes don't pass a single object, they pass an
array of items, each wrapped as `{json: {...}}`. A node runs once per item. This catches
people coming from Zapier, where a Zap fires once per record: port a Zap literally and you
get a node processing a batch where the original handled one thing at a time.

**Expressions** — `{{ }}` contains JavaScript evaluated against the current item.
`{{ $json.name }}` reads a field from the previous node's output. `{{ $json.body.name }}`
is what a webhook needs, because the POST body sits under `body`. Two things I learned the
hard way:

- `$json` always means the node immediately upstream, so it changes meaning when you insert
  a node. `$('Node Name').item.json.field` names the source explicitly and survives rewiring.
  On anything longer than three nodes, name the source.
- A missing field renders as the string `undefined`, not as blank. `{{ ($json.phone || '').trim() }}`
  is the fix, and it matters when the result is going into a prompt.

**Credentials** — stored encrypted by n8n, separate from the workflow. Exported workflow JSON
contains only a credential name and ID, never the secret, which is why this repo is safe to
publish. The encryption key lives in `~/.n8n/config`; lose it and every saved credential
becomes unreadable.

**Webhook: test URL vs production URL** — `/webhook-test/` only listens after you click
Execute workflow, accepts one call, and shows the data live on the canvas. `/webhook/` works
whenever the workflow is active and doesn't show anything on the canvas, so you read it in
the Executions tab. Build against the test URL, run against the production one.

**Error Trigger and error workflows** — a separate workflow starting with an Error Trigger
node, set as the Error Workflow in the main workflow's settings. It receives the failed
workflow's name, the node that failed, the error message and a link to the execution.

The catch: it only fires for production executions. Testing it with Execute workflow and the
test URL does nothing, which looks exactly like broken error handling.

**Retry and fallback** — separate node settings worth knowing apart. Retry On Fail handles a
service having a bad few seconds. A fallback model handles one being properly down. Neither
helps if the failure is in your own configuration, which is what the error workflow is for.

**Pinned data** — pin a trigger node's output and downstream nodes reuse it, so you can build
without re-firing the webhook every time. Easy to forget it's on and wonder why new test data
never appears.
