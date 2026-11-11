# Proposal review

How the user works the hub's open garden proposals with an agent: read every one, sort them into three tiers, and settle
the tiers in order, cheapest first. The
[application reference](https://github.com/paul-gross/blizzard/blob/master/docs/deployment/findings-and-proposals.md)
owns the proposal and finding lifecycle; this workflow owns the review session.

Use this when asked to run a proposal review or to go through, triage, or decide the hub's proposals. A workspace's
findings sweep can also use this review to settle its open proposals before drafting more.

Resolve the workspace's [binding](../connection.md), review scope, issue-filing process, and local marshalling policy
first. Review only the selected project's proposals unless explicitly authorized more broadly. The review only reads
code; proposal edits, accepts, passes, tracker writes, grouping, and promotion each stay within the user's approval.

## Roles and handoff

Keep a scratch decision manifest for the review: proposal IDs and session labels, evidence notes, approved dispositions,
grouping and promotion authorization, and pending versus verified results. Share its path with any delegated agents; do
not store session records or credentials in the usage repository.

The main agent orchestrates the review and owns the conversation and decision record. Explain the proposals with the
context, costs, tradeoffs, and recommendations the stages below require, and wait for the user's decisions. Where the
session permits delegated work, delegate inventory and verification to read-only review agents, and approved proposal
edits, accepts and passes, immediate marshalling, and hub readback to a dedicated execution agent. If delegation is
unavailable or the user stops it, perform those roles serially without weakening their approval boundaries.

Keep the next batch or heavy question prepared, including required verification, before the current answer arrives; hold
only one discussion ahead privately. On the user's answer, present that prepared next discussion immediately first, then
dispatch approved execution, verification, and further reads in the background. Execution handoffs, progress reports,
and grouping or promotion checkpoints must not delay the next discussion. Collect explicit grouping and promotion
approvals at a natural checkpoint after presenting it, batching pending rollouts where appropriate; waiting work remains
unauthorized until approved. Nonblocking completion notices must not repeatedly replace the pending question with
status-only turns.

Each execution handoff includes:

- Exact proposal IDs and session labels, the approved decisions and reshaping, and explicit exclusions.
- The review date and required closure reasons, plus shared manifest and notes paths for decisions and results.
- Current grouping and promotion authorization, including which lanes and additions it covers.

The execution agent applies only that authorization: no decisions on unapproved proposals, new scope, or inferred
promotion. It returns grouping proposals, uncertainties, blockers, and verified hub results to the main agent, not to
the user in a parallel conversation. The main agent relays material issues and approval requests, tracks pending versus
completed work, and keeps the review moving five at a time or one heavy decision at a time rather than ending with a
rollout status. Distinguish handed-off work from execution actually started and results verified.

## 1. Inventory

List the selected project's proposals, newest first:

```bash
blizzard hub garden-proposal list --project "$PROJECT" --tenant "$TENANT" --hub-url "$HUB_URL"
```

The open ones have no `closure`. Read every open body in full and the findings it cites, using
`garden-proposal get <proposal-id>` and `finding get <finding-id>` with the bound tenant and hub. Check each read's exit
status; a failed or partial inventory is a gap, not an empty docket. For a large set, authorized read-only review agents
can read batches, write notes to scratch files, and return tiers to the main agent.

## 2. Sort into three tiers

| Tier                      | Holds                                                                                                  |
| ------------------------- | ------------------------------------------------------------------------------------------------------ |
| **Obvious yes**           | A clear defect with a clear fix in one lane, needing no design decision; accepting it is plainly right |
| **For consideration**     | Worth doing or not on the user's judgment, such as a tweak, a new gate, or a scope or priority call    |
| **Requires much thought** | Turns on a design decision, crosses seams or repos, or conflicts with another proposal or the charter  |

Put each proposal in one tier with a one-line reason, and show the user the three lists before deciding anything. A
proposal that duplicates another open one, or work already accepted or in flight, is flagged wherever it lands: check
recently closed proposals, unfinished chunks, and open issues, since in-flight work can make a proposal moot.

## 3. Sweep-confirm the obvious yeses

Re-verify every obvious yes against the repositories' current configured base refs before it is accepted, unless the
user waives it. Use these verdicts for each cited finding:

| Verdict   | Meaning                                                     |
| --------- | ----------------------------------------------------------- |
| `present` | The defect is still there as described.                     |
| `partial` | The code changed, but the concern still partly applies.     |
| `gone`    | The code or text was removed or rewritten past the concern. |
| `unclear` | Not determinable with modest effort.                        |

Record the current locus and a line of evidence. Locate findings by their quoted text and symbols rather than trusting
old line numbers. A proposal with no findings still needs its own claims verified. Its body must still describe the code
as it stands. A proposal survives the sweep when its findings are still present and its fix direction still holds.
Anything that fails drops to **for consideration**, with the reason it failed.

Put the surviving list to the user. Accept only what they approve:

```bash
blizzard hub garden-proposal accept "$PROPOSAL" --reason "$REASON" --tenant "$TENANT" --hub-url "$HUB_URL"
```

Use the returned proposal ID and an approved reason naming the review and its date. This ordinary acceptance path mints
work that needs [marshalling](./marshalling.md); acceptance is not promotion. An explicitly requested acceptance without
a work item is a distinct disposition, not a way to schedule work. Passing a proposal requires the user's reason and
does not withdraw or resolve its findings.

**Group liberally when marshalling a review's chunks.** One accepted proposal is often a small fix, and a lane given
only that underuses its agent: mid-sized chunks do well. Group small chunks that share an area (one package, one feature
folder, one seam) into one lane, even when they are separable. Stop short of a sprawling PR. Keep a chunk standalone
only when it is already large, such as a broad mechanical sweep, and `depend` across groups only where marshalling's bar
for an edge is met. Promote the lanes in priority order only with the user's grouping and promotion approval. A later
tier's accepts join a lane that is still unacquired, or depend on it, within the authorized grouping. Bring additions
outside that scope back for approval; joining an already-ready lane also needs promotion authorization for the added
work, since grouping must not bypass that gate.

## 4. Walk the consideration tier

While the current five await the user's decisions, an authorized review agent can prepare the next five read-only. Keep
only one batch ahead, hold its prepared material privately, and present it after the user answers the current batch, not
alongside it. This preparation is separate from approved background execution and confers no approval or authority to
mutate proposals or the hub.

Present the tier five proposals at a time. Give each a short paragraph covering what is wrong, what the fix is, its
costs and tradeoffs, and a recommendation. Write for a senior software engineer, not an architect: say what the code
does and why it hurts, and leave out the harness's rule ids and layering vocabulary unless they carry the point. The
user calls each one *do* or *don't*, often with a reshaping:

- **Do.** Accept the proposal. When the user reshapes it, edit its title, body, and findings first, so the minted item
  carries the decision. Fold a proposal the user calls answered by another into that one, and pass it with a note naming
  where it went.
- **Don't.** Pass it with the user's reason, because a pass is what stops a later run from raising the same proposal
  again.

## 5. Take the heavy thinkers one at a time

Each proposal here gets its own discussion. Verify its claims against the current base refs first, since a heavy
thinker's premise is sometimes dormant or overstated. Then explain the background in plain terms, name the decision it
waits on, the options, and a recommendation, and settle it before moving to the next. Order the tier so decisions that
feed later ones come first.

The outcome is usually one of these:

- **Accept to the fleet** when the work is a rule plus well-understood fixes that fit one lane. Edit the body so it
  carries the user's decisions.
- **Pass, and file a tracker issue** when the work needs design first: a new capability, a new seam or contract, or a
  change to the domain model. Use the workspace's issue-filing process, write the user's design into it, and name the
  issue in the pass reason. A change that has to stay compatible across the skew window, such as emitting a new shape,
  then moving consumers onto it, then retiring the old one, becomes a small epic with one child per stage.
- **Pass** when the premise is dormant or the user declines. Record why, so a later run does not raise it again.

## 6. Report

Reconcile execution results with the shared manifest. Report the open count before and after, what was accepted with the
lanes it was marshalled into, what was passed and why, the issues filed, and what was left open and why. Name any
pending execution or blockers separately from completed changes.

## Don't

- Accept or pass any proposal before the user has approved that tier's list.
- Accept an obvious yes without re-verifying it against the current base refs, unless the user waived the
  re-verification.
- Pass a proposal without a reason.
- Treat passing a proposal as withdrawing its findings. Finding exits are separate decisions, not an implied consequence
  of a proposal disposition.

[Back to workflows](./index.md)
