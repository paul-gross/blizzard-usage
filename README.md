# blizzard-usage

Task-oriented Blizzard usage guidance for humans and agents. Start at [context/index.md](./context/index.md), then
follow the task you need.

This repository teaches use of the product. It does not contain live fleet state, configured worker prompts, graph
artifacts, or Blizzard's engineering conventions. The Markdown is the distributable source: clone or update it with Git,
without rebuilding or releasing Blizzard.

## Adopt it

Keep an adopted checkout separate from the worktree used to edit the guidance. Link its `index.md` from your workspace's
agent entry point and put your deployment binding beside that link;
[context/connection.md](./context/connection.md#workspace-binding) defines the binding.

Winter workspaces can declare both roles in `.winter/config.toml`:

```toml
[[project_repository]]
name = "blizzard-usage"
url = "https://github.com/paul-gross/blizzard-usage.git"

[[standalone_repository]]
name = "blizzard-usage"
url = "https://github.com/paul-gross/blizzard-usage.git"
path = ".winter/ext/usage"
```

Run `winter ws init --all` to reconcile. The context-only manifest lets Winter discover `index.md`; the standalone is
the adopted copy and the project worktrees are for edits. No README entry override is needed. Without Winter, a normal
clone and a link to `index.md` work just as well.

Update adopted checkouts deliberately through your workspace's Git tooling, reviewing the diff or pinning a revision
when reproducibility matters. Existing sessions may need to reread changed guidance. Updating these files does not
upgrade a hub, CLI, or runner.

## Maintain it

Read [CONTRIBUTING.md](./CONTRIBUTING.md) before changing the corpus. This Winter extension is governed by
`winter-context` and `winter-canon`; its task pages teach product usage, not repository engineering policy.
