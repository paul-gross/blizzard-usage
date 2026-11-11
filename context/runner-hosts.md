# Operate the runner at the right boundary

Use [connection.md](./connection.md) to establish the machine, session role, and authority first. Hub visibility does
not imply shell access to a runner host. A local checkout of the application is not its installed runtime directory.

## Inspect from where you are

From an operator session, use the bound hub to read registrations and reported availability:

```sh
blizzard hub runner list --tenant "$TENANT" --hub-url "$HUB_URL"
blizzard hub runner get "$RUNNER" --tenant "$TENANT" --hub-url "$HUB_URL"
blizzard hub runner-host get "$HOST" --tenant "$TENANT" --hub-url "$HUB_URL"
```

Check the latest report, projects served, harness/capability compatibility, available capacity, held chunks, and brake
state. A registration or old heartbeat is not proof the host is reachable now. A paused claimed chunk can still consume
a slot; its consequences are in [work control](./work/control.md).

On the actual host, with the workspace's approved local invocation, read its runtime directly:

```sh
blizzard runner status --dir "$RUNTIME_DIR" --runner "$RUNNER"
```

The host owns `RUNTIME_DIR`; do not infer it from this repository's location. The host's local API/panel and its
hub-facing registration are different surfaces. Local status can report what the runner holds even with the hub
unreachable. If you are on another machine, use authorized hub reads or request host-operator assistance instead of
trying this machine's loopback address.

## Choose a brake, not a vague “pause the fleet”

| Control                            | Effect                                                                                                                                            |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| `blizzard hub runner pause RUNNER` | Stops that runner claiming new work. Existing chunks continue. Clear with hub `runner resume`.                                                    |
| `blizzard runner pause`            | On the selected local runner, blocks new worker processes, including resumes/respawns; running workers continue. Clear with local `runner start`. |
| Per-chunk control                  | Use [work control](./work/control.md) when one chunk, rather than admission, must be interrupted.                                                 |
| Host daemon restart                | Interrupts workers managed by that daemon and must follow the machine's deployment/recovery runbook. Not a chunk retry.                           |

Neither runner brake is a drain. They are independent; clearing one does not clear the other. The local command must
select the intended host/runtime and runner, particularly on a multi-runner host. Hub commands use the explicit bound
tenant and URL.

## Change a host only with its runbook

Confirm the daemon's owner, service manager, runtime directory, deployed version, held sessions, and all affected
projects before a restart or upgrade. Never kill by a broad process-name pattern, start a second daemon to test
reachability, or reset held environments to free capacity.

A graceful restart can interrupt every worker on the host, including the session issuing it. Finish durable outputs
first and plan the runbook's post-restart health/version checks. Recovery depends on the deployed runtime and a
successful restart, not on this Markdown. If installation, migration, or configuration checks fail, stop before
restarting onto the broken installation and report the failure. Credential enrollment, rotation, retirement, and service
installation belong to authorized deployment setup, not routine chunk inspection.

[Back to usage](./index.md)
