# Prepare work to run

Use the workspace binding and local work policy from [connection.md](../connection.md). The product's ability to ingest or promote something is not approval to run it. A workspace may require review, a manual-only lane, or a particular scheduling policy.

## Check the destination

Read the project before creating work:

```sh
blizzard hub project get "$PROJECT" --tenant "$TENANT" --hub-url "$HUB_URL"
blizzard hub chunk list --project "$PROJECT" --tenant "$TENANT" --hub-url "$HUB_URL"
```

Confirm that the work source and landing repositories are linked to that project, the intended graph is available, and existing chunks do not already own the work. Tenant-owned sources and repositories are not automatically linked to every project. A Git clone or a new workspace repository declaration does not register or link anything in the live hub. Ask for separate authorization when configuration is missing.

## Shape the chunk

Ingest work-item URLs or the source-qualified references accepted by the installed command. `POINTER` below is a verified reference from the intended source. A batch of pointers mints one chunk, so batch only work intended to run together.

```sh
blizzard hub chunk ingest "$POINTER" --project "$PROJECT" --tenant "$TENANT" --hub-url "$HUB_URL"
```

Read back the returned chunk before proceeding. Ingest is not the same as promoting work into the ready queue.

| Operation                             | Choose it when                                                                                                                                               |
| ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `chunk group SURVIVOR MERGED...`      | Unclaimed, unfinished chunks in the same project should become one unit. The survivor gains their items and dependencies; this is not an ordering operation. |
| `chunk depend DEPENDENT PREREQUISITE` | One chunk must wait until another is done. The dependent is the first argument.                                                                              |
| `chunk promote CHUNK`                 | The work is approved and sufficiently prepared to become claimable.                                                                                          |
| `queue move CHUNK POSITION`           | You are changing ready-queue priority, not declaring a prerequisite. Position zero is the front.                                                             |

Group only after checking each chunk's scope and dependency edges. The product refuses invalid states and dependency cycles; do not bypass refusals by minting duplicate work. A prerequisite blocks until it is **done**: stopping it does not satisfy the dependent, and hand-completing it just to unblock work misrepresents its outcome.

Declare real prerequisites before promotion:

```sh
blizzard hub chunk depend "$CHUNK" "$PREREQUISITE" --tenant "$TENANT" --hub-url "$HUB_URL"
blizzard hub chunk promote "$CHUNK" --tenant "$TENANT" --hub-url "$HUB_URL"
blizzard hub queue get --project "$PROJECT" --tenant "$TENANT" --hub-url "$HUB_URL"
```

Queue order is not a dependency guarantee. Ready work still needs an eligible runner with capacity, compatible capabilities, and no remaining holds. Inspect those facts through [inspect.md](./inspect.md) if a promoted chunk does not start. Reordering may affect other projects sharing the tenant's queue; inspect the full queue and local priority policy before an authorized move.

[Back to work](./index.md)
