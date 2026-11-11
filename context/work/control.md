# Choose the intervention by its consequence

First [inspect the chunk](./inspect.md), verify its project, and confirm authority for the change. These are mutations, not diagnostic probes. Check the installed verb's `--help` and reread the chunk immediately before acting.

## Select the smallest correct control

| Control               | What it means                                                                                                                                                                                                     |
| --------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `chunk pause CHUNK`   | Interrupt a claimed chunk's worker while retaining the claim and environments, or hold an unclaimed ready chunk out of the queue. Claimed pauses still occupy capacity. Refused on delivering or terminal chunks. |
| `chunk resume CHUNK`  | Lift the chunk's pause and let its parked session continue in place. Does not clear another kind of hold.                                                                                                         |
| `chunk requeue CHUNK` | Close an open escalation and retry at the current node at a fresh epoch. Use after addressing why it escalated, not to release its claim.                                                                         |
| `chunk restart CHUNK` | Force fresh-session re-entry at the current node, or the explicitly chosen node/graph. Keeps the claim and on-disk work, not the displaced session.                                                               |
| `chunk detach CHUNK`  | Release a live claim and held environments without completing the chunk. Does not clear escalation or other holds; not a synonym for requeue.                                                                     |
| `chunk stop CHUNK`    | End the chunk as abandoned and release its claim. Irreversible, not a retry or an undo of already-landed work.                                                                                                    |
| `chunk done CHUNK`    | Hand-complete the chunk, release its claim, and request closure of its work items. May complete a stopped chunk; this bypasses remaining graph steps and is not proof of delivery.                                |

Choose requeue when a corrected escalation should rerun the current node. Choose restart only when clean re-entry is intended; changing `--node` or `--to-graph` can bypass work and needs its own justification. Restart supersedes open interactions at the displaced position. A standing chunk pause still needs to be lifted before execution continues.

An operator note on requeue or restart explains the resolved problem to later sessions:

```sh
blizzard hub chunk requeue "$CHUNK" --note "$NOTE" --tenant "$TENANT" --hub-url "$HUB_URL"
```

`NOTE` should contain the actual resolution, not a generic instruction to try harder. These notes become part of the chunk's continuing context. Use the same `--tenant` and `--hub-url` leaf options with the other hub controls.

## Verify the effect

Read the chunk and relevant events again after an accepted change. Hub acceptance and runner reconciliation are separate: an interruption or respawn may occur on the next tick. On a refusal, refresh the state and understand the reason instead of trying neighboring verbs until one succeeds.

Answering a question does not unpause a chunk. Releasing a claim does not settle a gate. Ending a chunk is not a substitute for repairing delivery; [delivery.md](../delivery.md) owns that diagnosis.

For a fleet-wide admission or machine issue, use [runner-hosts.md](../runner-hosts.md). Runner brakes and daemon restarts are different controls from these chunk verbs.

[Back to work](./index.md)
