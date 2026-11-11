# Understand your role

Blizzard moves work through a graph of steps. A **chunk** is the unit it schedules, holding one or more work items from a tracker or the hub. A chunk belongs to one project inside a tenant. The graph determines what runs next, including worker steps, hub-executed steps, and human decisions.

| Component   | What it does                                                                          |
| ----------- | ------------------------------------------------------------------------------------- |
| Hub         | Owns shared work state, graph progress, questions, decisions, and delivery.           |
| Board       | A human view of the hub, scoped to a tenant and optionally a project.                 |
| Runner host | A machine-local daemon hosting one or more runners and their runtime resources.       |
| Runner      | Claims eligible chunks for its projects and manages worker sessions and environments. |
| Worker      | An agent session running one assigned node-step under a lease.                        |
| CLI         | Offers hub-facing operator commands and runner-facing local or worker commands.       |

## Identify this session

| Role                                    | How to proceed                                                                                                         |
| --------------------------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| Human operator or interactive assistant | Use the workspace's authorized hub binding and operator interfaces. A checkout of Blizzard does not make you a worker. |
| Leased fleet worker                     | Use the assignment and injected worker interface; enter [workers.md](./workers.md).                                    |
| Host operator                           | Use the named machine's runbook and authority; enter [runner-hosts.md](./runner-hosts.md).                             |

A task to inspect work is not permission to change its graph, priority, credentials, or host deployment. Establish the target and permitted operations through [connection.md](./connection.md) before acting.

## Guidance is not runtime state

This corpus explains how to use the interfaces. Live chunk state, graph declarations, published artifacts, configured prompts, assignments, and human answers come from the fleet itself. Do not substitute an example here for the assignment's required output or copy these files into live prompt/artifact records as an adoption step.

The workspace owns its local policy and deployment binding. The application owns exact command/API contracts. Blizzard's engineering harness governs changes to its code; this usage corpus does not replace it.

[Back to usage](./index.md)
