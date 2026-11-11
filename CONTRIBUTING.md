# Maintain the usage corpus

## Content ownership

This extension follows `winter-context:/index.md` for shared engineering governance and `winter-canon:/index.md` for agent-facing Markdown. Author guidance directly in Markdown under [context/](./context/index.md). The root [index.md](./index.md) is the extension discovery entry and routes to that hub.

Keep exact command schemas and implementation/deployment details in the [Blizzard application](https://github.com/paul-gross/blizzard). Verify behavior against its current operator docs and exported `contracts/cli/{hub,runner}.json`, then check applicability to deployed versions. Prefer a small task example to a copied command reference. Do not embed this corpus as hand-maintained application strings or require a CLI release to ship a prose edit.

Workspace-specific addresses, tenant/project identities, runtime paths, machine responsibilities, credentials, and local approval policy stay in the adopting workspace. Public examples use named shell variables. Runtime prompts, graph declarations, assignments, and published system artifacts remain runtime data rather than a second copy of this corpus.

## Verification

Use the consuming workspace's Markdown checks. The repository supplies formatting configuration, not its own validation scripts or application test harness. With the standard tools available:

```sh
dprint check
rumdl check .
```

Review links and task routing, and compare changed command examples and behavioral claims with the application's exported CLI contracts and operator docs. Do not execute examples against a live fleet to validate prose. Keep delivery evidence outside the usage corpus.

## Delivery and adoption

Use scoped Conventional Commits and a PR to `master`. Changes publish through Git, not an application build or release. Review the adopted revision separately from the editing worktree; do not assume updating a worktree refreshes the workspace's standalone clone or an already-running session. [README.md](./README.md) describes adoption.
