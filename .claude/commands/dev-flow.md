---
name: dev-flow
description: Alias for dev-orchestrator — start or continue multi-phase work
---

Invoke the agent-skills:dev-orchestrator skill (alias dev-flow).

Resolve state and overlay paths for the active tool using
`docs/dev-orchestrator-paths.md`.

For a **new task**, start from intake and create index + session state.
For **continue**, resume when the user names a task — read index and session,
confirm phase and exact next action, then proceed.
