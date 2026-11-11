# Answer the interaction that is actually open

A worker question and a human gate decision are different records. Both can make work wait, but an answer to one cannot resolve the other. Establish the target through [connection.md](../connection.md), then list the project's open interactions:

```sh
blizzard hub question list --project "$PROJECT" --tenant "$TENANT" --hub-url "$HUB_URL"
blizzard hub decision list --project "$PROJECT" --tenant "$TENANT" --hub-url "$HUB_URL"
```

Match the interaction's chunk and node to the work you intend to steer. Read the question or gate, its evidence, prior answers, and operator notes in the chunk detail before responding. Use the actual returned interaction ID; do not infer one from a chunk ID.

| Interaction         | How to respond                                                                                                                                    |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| Worker question     | Supply a direct answer that settles the uncertainty the worker raised. It becomes continuing context for later sessions.                          |
| Human gate decision | Choose one of the decision's offered choices after reviewing the evidence; the choice selects the graph's route. A note can explain the decision. |

For a question, `ANSWER` is the intended human response:

```sh
blizzard hub question answer "$QUESTION" "$ANSWER" --tenant "$TENANT" --hub-url "$HUB_URL"
```

For a gate, `CHOICE` is a value read from that specific open decision, not an invented synonym such as “continue”:

```sh
blizzard hub decision resolve "$DECISION" "$CHOICE" --note "$NOTE" --tenant "$TENANT" --hub-url "$HUB_URL"
```

A gate may be declared by the graph or imposed by a runner's configuration. Read the origin and check evidence. A choice that overrides failed checks is a substantive approval decision, not a harmless acknowledgement.

Only the first answer or resolution counts. If somebody else resolved it or a restart closed it, reread the accepted result and current chunk state; do not create a competing interaction. Confirm the accepted response and subsequent progress. Runner reconciliation may take a tick, and independent holds can remain; use [control.md](./control.md) only when that separate intervention is justified.

If you cannot settle the question or lack authority to choose, ask the responsible person rather than inventing an answer to clear the wait.

[Back to work](./index.md)
