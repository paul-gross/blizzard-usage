# Tending the garden

When the user asks to tend the garden, an operator-driven session lists every routine with the date each of its scopes
was last swept, recommends which pairs to run, and runs the pairs the user picks. It then marshals the chunks those runs
mint into `ready`. The user approves twice: **which runs to start**, and **whether the proposed grouping and promotion
order may be applied**. Nothing is written to the hub before each approval.

A cadence keeps a routine on its own schedule, but a routine no cadence keeps runs only when someone starts it. Without
a standing pass, the routines nobody remembers go unswept the longest.

Resolve the adopting workspace's [binding](../connection.md) and gardening policy first. Work within the selected
project; do not silently widen a project-scoped request to the tenant. The workspace supplies scope ground, criteria
owners, repository base refs, and any additional exclusions. This pass mints runs and never fixes code itself. Check the
installed commands' `--help`; the
[application reference](https://github.com/paul-gross/blizzard/blob/master/docs/deployment/routines-and-scopes.md) owns
their exact contracts.

## 1. Inventory

- **Routines.** `hub routine list` lists the live routines. Confirm each row belongs to the selected project.
- **Scopes and last sweep.** For each routine, `hub routine get <routine-id>` returns its linked scope set and its
  default scope. `hub routine sweeps <slug> --since <start> --until <end>` returns the routine's last-swept table. That
  table ignores the window and covers every linked scope, showing either the produced-at instant and recorded revisions
  or `never`. The window bounds only the measurement series that follows the table.
- **Cadence-kept pairs.** `hub cadence list` lists the live cadences. For each one, `hub cadence get <slug>` names each
  chore's routine, and `hub cadence errands <slug>` lists its errands, one per chore and scope, with each one's state,
  last run, and `due_at`. A pair with an errand is kept by its cadence and is not one to start by hand. Both verbs say
  on stderr when the hub's `cadences` flag is off; then a cadence mints nothing, and no pair is kept.
- **Runs in flight.** `hub run list --since <start> --until <end>` lists runs in the chosen window. Correlate their
  chunks with [unfinished work](../work/inspect.md); extend the window when it would miss an older active run. An
  undelivered, nonterminal run still holds its pair, including while paused or waiting on a person. Do not start it
  again.
- **Check every call.** A failed call prints nothing, and that looks exactly like a routine with no sweeps. Check each
  exit status. A pair whose call never succeeds is a gap in the inventory, not a `never`.

Give every project-scoped read the bound `--project`, `--tenant`, and `--hub-url`. JSON output may help assemble the
inventory, but an empty or incomplete response is not evidence that the inventory is complete. Choose explicit time
windows from the workspace's policy or the operator's request; a recent-runs window alone cannot prove no run is active.

## 2. Show everything, then recommend

Show the user one table with every routine/scope pair: routine, scope, last swept (or `never`), the age of that sweep,
whether a run is in flight, and the cadence that keeps it with its `due_at`. Sort it oldest first, with `never` at the
top. Show every pair, including the ones you don't recommend.

Then recommend which pairs to run, giving each recommendation its reason. Weigh these:

- **Never swept.** The pair has no baseline, so it runs `--mode full`.
- **Ground has moved.** Commits have landed on the repository's configured base ref in the scope's ground since the
  recorded revision. Many commits on a pair that is not yet old still make it worth running.
- **Criteria have moved.** The axis's criteria docs, identified by the target workspace's gardening registry, changed
  since the last sweep. A changed standard reopens judgment on code that didn't change, so the run is `--mode full`.
- **Age.** Use age when nothing else separates two pairs.

Otherwise, a pair with a baseline defaults to `delta`. Leave out pairs whose run is still in flight, pairs a cadence
keeps, and pairs with no ground and no criteria change since their last sweep. A cadence-kept errand that is stopped,
named by its attention, is the user's to look into on the board; name it rather than running its pair by hand.

**Ask: which ones do we run?** Offer the recommended set, and accept any subset or addition from the full table. The
user's pick is final.

## 3. Run

For each chosen pair, run:

```bash
blizzard hub routine run "$ROUTINE" --scope "$SCOPE" --mode "$MODE" --project "$PROJECT" --tenant "$TENANT" --hub-url "$HUB_URL"
```

`ROUTINE` is the inventoried routine slug, `SCOPE` its chosen linked scope, and `MODE` the approved `full` or `delta`.

- Record each minted `chunk_id`, and record whether a requested `delta` was downgraded to `full`.
- A `run refused` conflict usually means a run is already live for that pair. Report it and don't retry it.
- Every minted chunk rests `not_ready` until it is promoted.

## 4. Draft the marshal

Draft the mapping, grouping, and dependency phases of [marshalling](./marshalling.md) under the workspace's local
policy, without writing to the hub. Map the new chunks against every unfinished chunk, and propose the groups, the
dependency edges, and a promotion order. Then show the draft: each group with the seam it shares, each edge with its
reason, and the promotion order with the reason for each place. A run edits no code, so collisions here come from shared
scope ground, not from files two lanes would both edit.

**Ask: are we ready to group these this way and promote?** This approval covers grouping, dependency changes, and
promotion for these chunks. They wait for it instead of being applied unasked. Adjust the draft until the user approves
it.

## 5. Apply, then report

Once the user approves, run `hub chunk group`, `hub chunk depend`, and `hub chunk promote` in the approved order, each
according to [marshalling.md](./marshalling.md).

Then report to the user:

- each run started: routine, scope, mode (and any downgrade), and chunk id
- each pair skipped, and why
- each group, edge, and promotion, with its reason

The runs' findings arrive on the hub as each run delivers. Draining them belongs to the workspace's findings-sweep pass,
where configured, and [proposal review](./proposal-review.md). Starting a run is not evidence its sweep delivered or its
findings were settled.

## Don't

- Hide pairs that you don't recommend. The user picks from the whole table.
- Treat a failed inventory call as `never`, or start a second run on a pair whose run is still in flight.
- Recommend starting a pair a cadence keeps by hand.
- Start a run, group a chunk, or promote one before the user approves that step.

[Back to workflows](./index.md)
