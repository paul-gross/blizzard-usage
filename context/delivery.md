# Diagnose landing without bypassing the graph

Read the chunk's Delivery view, current node, submitted commits, repository landing facts, and linked forge changes.
[Work inspection](./work/inspect.md) describes how to gather the surrounding evidence. A PR being open does not by
itself mean an operator should merge it.

## Establish what is supposed to happen

Each repository has a landing policy. A delivery submission captures its repository terms at its first turn, so a
configuration edit is not proof an in-flight submission changed policy or landing order.

| Policy            | Expected landing                                                                                                                 |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| `merge-on-checks` | The hub opens/updates a pull request and merges after its checks pass.                                                           |
| `fast-forward`    | The hub proves checks on a revision and advances the base without a PR. A diverged base may require a checked landing candidate. |
| `park`            | A person reviews and merges the linked PR; the hub polls for the result.                                                         |

Read the repository and the chunk's flow record using IDs returned by inspection:

```sh
blizzard hub repo get "$REPOSITORY" --tenant "$TENANT" --hub-url "$HUB_URL"
blizzard hub flow get "$FLOW" --tenant "$TENANT" --hub-url "$HUB_URL"
blizzard hub flow get "$FLOW" --record --tenant "$TENANT" --hub-url "$HUB_URL"
```

A flow is one repository branch's landing line, shared across projects. A chunk may be waiting its turn rather than
running a failed merge. Read its current place, latest turn outcome, and next poll time before intervening.

## Classify the wait

| Evidence                                   | Next step                                                                                                                                                                 |
| ------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Waiting behind earlier work                | Inspect the earlier submission and eligibility; do not change chunk queue priority as a delivery fix.                                                                     |
| Pending checks                             | Inspect the exact submitted/candidate revision on the forge and whether the correct CI triggers ran.                                                                      |
| No checks reported                         | Treat as absent evidence, not green checks. Check the deployed build's behavior and repository CI policy; report an indefinite wait rather than assuming it will resolve. |
| Red checks                                 | Read failure output and the hub's routed repair/escalation outcome. Do not manually declare success.                                                                      |
| Conflict or changed head                   | Read the recorded reason and repair route; avoid pushing extra work onto an automatic delivery branch.                                                                    |
| Awaiting a human merge                     | Confirm the captured policy and linked PR before an authorized forge merge.                                                                                               |
| Permission, capability, or timeout failure | Preserve the diagnostic evidence and request the corresponding configuration or operator repair.                                                                          |

Do not switch landing policy or disable required checks just to make a wait disappear. Such changes need explicit
approval and may not affect this submission. Separate the observed runtime behavior from fixes present only in a newer
source checkout or guidance revision.

## When a manual merge is intended

For parked delivery, or an explicitly authorized manual recovery, inspect the linked PR, target branch, head,
review/check evidence, and repository-specific merge policy on the forge. Merging is an external mutation: follow the
consuming project's approval and merge rules rather than assuming one universal merge strategy.

After the merge, verify the forge's actual merged state and landed revision, then let the hub observe it. Recheck
delivery evidence and graph progress. Repositories can land independently, so a partly landed chunk may still have an
open PR elsewhere. Closing a PR without merging is not a landing.

A manual merge and completion of the full Blizzard graph are different milestones. Subsequent steps, such as
retrospective, may remain. Do not use `chunk done` just to clear `delivering`; it is the explicit hand-completion
described in [work control](./work/control.md), not a refresh button. If the hub does not observe a confirmed merge,
report the chunk, PR, revision, poll/event evidence, and deployed version before choosing a recovery.

[Back to usage](./index.md)
