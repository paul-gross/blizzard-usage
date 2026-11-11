# Connect to the intended fleet

Resolve the workspace's deployment binding before making a fleet call. CLI defaults are not a binding: a default loopback address may refer to nothing, or to the wrong service on this machine.

## Workspace binding

The adopting workspace supplies these facts, in its own context rather than this repository:

| Fact                       | What the binding must establish                                                                      |
| -------------------------- | ---------------------------------------------------------------------------------------------------- |
| Hub                        | The intended API/board base URL and whether it is production, test, or a local development hub.      |
| Tenant and project         | The tenant identity and project identity used for this task; a name alone may be ambiguous.          |
| Session role and authority | Whether this is an interactive assistant, worker, or host operator, and which actions are permitted. |
| CLI access                 | The approved executable/invocation and authentication procedure.                                     |
| Machine ownership          | Whether a runner host is reachable here and where its runbook lives.                                 |
| Local work policy          | Any approval, readiness, priority, or manual-only restrictions beyond the product's capabilities.    |

An agent entry can eagerly load the small usage router and point to the local binding, for example:

```markdown
- IMPORTANT: @.winter/ext/usage/index.md — how to use Blizzard
- Before fleet calls, read context/blizzard-binding.md for this workspace's target and authority.
```

Those paths are illustrative and belong to the consuming workspace. Keep credentials in its approved secret/session storage, not in Markdown. Do not print tokens while diagnosing access.

If the binding, authentication, or CLI is missing, ask the operator for setup. Do not guess localhost, search sibling checkouts for an executable or credentials, install tools, start a daemon, or enroll a runner to make an inspection work.

## Check access and scope

Use the approved CLI's `--version` and the relevant command's `--help` locally. Compare its version with the hub's reported version from the approved health surface or board. A Git update of this corpus proves neither that a command exists in your installed CLI nor that the hub supports it. If they disagree, stop at the unsupported operation and request a supported invocation or upgrade; do not upgrade a live service on your own.

The examples use shell variables supplied from the binding: `HUB_URL`, `TENANT`, and `PROJECT`. `CHUNK` and other resource variables come from verified reads. They are not built-in environment variables. Set them explicitly in your shell before using an example; do not copy an unset placeholder into a live call.

For a hub requiring human sign-in, follow its approved login flow:

```sh
blizzard hub login --hub-url "$HUB_URL"
```

Use `--paste` only when the approved headless flow needs it. A human session and a runner's machine token are different identities; never borrow the runner token to bypass human access. A permission refusal calls for the right access, not a different identity.

Start with a scoped read:

```sh
blizzard hub project get "$PROJECT" --tenant "$TENANT" --hub-url "$HUB_URL"
blizzard hub status --project "$PROJECT" --tenant "$TENANT" --hub-url "$HUB_URL"
```

Confirm the returned project, linked sources, and repositories match the task. Project-scoped list commands accept `--project`; commands addressing a chunk by ID generally do not. Read the chunk and verify its project before a mutation. The board's project lens is a view filter, not a different authorization boundary.

On connection or authorization failure, report the target, command, and sanitized error. Preserve the distinction between unreachable hub, expired login, denied permission, and unknown resource; none justifies silently switching targets.

[Back to usage](./index.md)
