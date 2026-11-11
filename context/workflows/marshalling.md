# Marshalling the backlog

To **marshal** is to turn a resting backlog into ordered, claimable work. A request to marshal includes promotion within
the authorized scope, subject to the workspace's exclusions and approval checkpoints. A request only to draft or prepare
work does not authorize promotion.

Use the adopting workspace's binding to establish the hub, tenant, project, permitted backlog, and local policy.
Selection rules, manual-only markers, release restrictions, priority, and required approvals belong there. If a missing
policy changes which work may run or which writes are authorized, settle it with the operator before those writes. Do
not borrow another workspace's labels or tenant-wide authority.

[Work preparation](../work/prepare.md) owns command syntax, project-link prerequisites, and the consequences of
ingestion and scheduling controls. This workflow owns the sequence and reasoning for a marshalling pass. It does not
require host access or a fleet-worker identity.

## Map the ground

For each selected `not_ready` chunk, read its work items and locate the code and docs it will change on the
repositories' configured base branches. Map unfinished work too, including chunks already ready, running, paused,
escalated, or delivering: resting work may build on or substantially rewrite ground held by an in-flight chunk. Use
[work inspection](../work/inspect.md) for the evidence.

When the request includes ingestion, check for existing ownership before minting anything. An authorized chain can be
ingested together when its prerequisites are landed or represented by work that can become chunks; use dependency edges
rather than waiting to ingest each successor until its predecessor lands. Work waiting on an external event no chunk
represents, such as a release cut or a manual deployment, stays uningested until the event occurs. Local exclusions
still apply to every link.

Newly minted chunks need the same mapping, grouping, and dependency assessment before promotion. A workspace can require
that preparation immediately on minting; it must separately define whether promotion is authorized.

## Group one change

Group chunks that are one change: the same defect from two angles, or small changes to the same ground for the same
reason. The resulting chunk should fit one worker lane and a reviewable delivery, not a sprawling bundle. Some workspace
passes may explicitly authorize broader grouping of small related fixes; follow their declared boundary.

Check that every chunk is unclaimed, unfinished, and in the same project before grouping. Do this before promotion, so
two lanes do not build the same change. Do not fold in-flight work into a new group or use grouping to smuggle
unapproved work into a ready chunk.

## Link genuine prerequisites

A chunk must follow another when it builds on what the other introduces: it calls, extends, or tests code that does not
exist yet. Ordering is also warranted when both massively restructure the same files, so whichever lands second would
have to redo its work rather than resolve a normal rebase.

Sharing files alone is not enough. Edits to separate regions, additive changes, annotation and typing sweeps, renames a
rebase can follow, and tests or docs beside a rewrite can proceed in parallel. An ordinary merge conflict is not proof
the work needed serialization. Each unnecessary edge leaves a lane idle until its prerequisite finishes.

Put the foundation first; for otherwise equivalent competing rewrites, put the smaller first. Apply the same test to
dependencies on in-flight work. Declare the dependent first and the prerequisite second, check for cycles, and read back
the resulting edges before promotion.

Reconcile tracker prerequisites under the workspace's tracker policy. Where it requires both tracker links and hub
edges, keep them aligned; do not silently erase a human-authored tracker dependency merely because the two systems
disagree. Record the reason for each correction and obtain any required approval.

## Promote in priority order

Once grouping and dependency assessment are complete, promote eligible chunks in the workspace's intended priority
order. A dependent can be ready while blocked on its prerequisite; queue position is not a substitute for the dependency
edge. Excluded or unapproved work stays unpromoted, even if the user broadly asked to marshal.

## Reorder only where needed

Inspect the resulting queue. Move a ready chunk only where new work must jump work already ready, not merely to repeat
an order already established by promotion. The queue is shared across projects in the tenant: a project-scoped request
does not authorize reprioritizing another project's work.

## Report the result

Report the ingested work, surviving groups, dependency edges, promotions, and queue moves, with the reason for each. For
an edge, name what the dependent builds on or the rewrite that requires ordering. Include excluded work and external
prerequisites, the chunks they hold back, and any decision still needed from the operator.

Read back the resulting groups, dependencies, statuses, and queue rather than treating command acceptance as proof of
the final arrangement. Distinguish a proposed plan from mutations actually applied.

[Back to workflows](./index.md)
