# Dev Orchestrator — Paths by Tool

The dev-orchestrator skill uses **logical paths** (defined in
[`skills/dev-orchestrator/SKILL.md`](../skills/dev-orchestrator/SKILL.md)).
This document maps those paths to concrete locations for each AI tool.

Runtime state and user overlays live **outside any git repo** — they are personal
and resumable across sessions.

---

## Logical paths

| Logical path | Purpose |
|--------------|---------|
| `<agent-state-root>/dev-orchestrator/index.md` | Global task index |
| `<agent-state-root>/dev-orchestrator/sessions/<task>.md` | Per-task session state |
| `<user-overlays-root>/overlays/<repo-key>/SKILL.md` | Repo-specific orchestrator overlay |

**Repo key** detection (same for all tools): explicit name in the user's message →
git remote slug (e.g. `houzz/visual-tools` → `visual-tools`) → workspace folder name.

**Templates** (committed, read-only): `skills/dev-orchestrator/templates/`

---

## Canonical default (recommended)

When your tool is not listed below, or you have no existing layout, use:

| Logical | Path |
|---------|------|
| Agent state root | `~/.agent/` |
| Task index | `~/.agent/dev-orchestrator/index.md` |
| Session files | `~/.agent/dev-orchestrator/sessions/` |
| User overlays root | `~/.agent/` |
| Overlay file | `~/.agent/overlays/<repo-key>/SKILL.md` |

Create directories on first use. This layout is tool-neutral and works from any
agent that can read the user's home directory.

---

## Per-tool paths

### Claude Code

| Logical | Path |
|---------|------|
| Agent state root | `~/.agent/` |
| Task index | `~/.agent/dev-orchestrator/index.md` |
| Session files | `~/.agent/dev-orchestrator/sessions/` |
| User overlays root | `~/.agent/` |
| Overlay file | `~/.agent/overlays/<repo-key>/SKILL.md` |

Slash commands: [`.claude/commands/`](../.claude/commands/) (`/dev-orchestrator`,
`/dev-flow`, `/dev-flow-tasks`).

Skills from this repo are typically installed via Claude plugin or `gh skill`.

### Cursor

Cursor may use the canonical `~/.agent/` layout, or the Cursor-native paths below
if you already keep personal skills under `~/.cursor/skills/`.

| Layout | Agent state root | Overlay file |
|--------|------------------|--------------|
| **Canonical (recommended)** | `~/.agent/` | `~/.agent/overlays/<repo-key>/SKILL.md` |
| **Cursor-native (optional)** | `~/.cursor/dev-orchestrator/` | `~/.cursor/skills/overlays/<repo-key>/SKILL.md` |

Pick one layout and stay consistent. To migrate from Cursor-native to canonical:

```bash
mkdir -p ~/.agent/dev-orchestrator ~/.agent/overlays
# state
[ -d ~/.cursor/dev-orchestrator ] && cp -R ~/.cursor/dev-orchestrator/* ~/.agent/dev-orchestrator/
# overlays
[ -d ~/.cursor/skills/overlays ] && cp -R ~/.cursor/skills/overlays/* ~/.agent/overlays/
```

Slash commands: add equivalents under `.cursor/commands/` in your project, or
invoke the skill from user rules.

Personal skills directory: `~/.cursor/skills/` (do not use `~/.cursor/skills-cursor/`).

### Gemini CLI

| Logical | Path |
|---------|------|
| Agent state root | `~/.agent/` |
| Task index | `~/.agent/dev-orchestrator/index.md` |
| Session files | `~/.agent/dev-orchestrator/sessions/` |
| Overlay file | `~/.agent/overlays/<repo-key>/SKILL.md` |

Slash commands: [`.gemini/commands/`](../.gemini/commands/) in this repo (TOML format).

### Antigravity CLI

| Logical | Path |
|---------|------|
| Agent state root | `~/.agent/` |
| Task index | `~/.agent/dev-orchestrator/index.md` |
| Session files | `~/.agent/dev-orchestrator/sessions/` |
| Overlay file | `~/.agent/overlays/<repo-key>/SKILL.md` |

Slash commands: [`commands/`](../commands/) in this repo (TOML format). See
[antigravity-setup.md](./antigravity-setup.md).

### Any other Markdown-aware agent

Use the **canonical default** (`~/.agent/`). Point the agent at
`skills/dev-orchestrator/SKILL.md` and this file for path resolution.

---

## Repo-local overlays (optional, team-shared)

For multi-contributor repos, a team may commit overlays inside the project:

| Path | Scope |
|------|-------|
| `.agent/overlays/<repo-key>/SKILL.md` | Team-shared; checked into git |

**Load order** when both exist:

1. Repo-local overlay (`.agent/overlays/…`)
2. User overlay (`<user-overlays-root>/overlays/…`)

User overlays extend or override team defaults only when the skill explicitly allows it;
prefer repo-local for team conventions, user-local for personal workflow tweaks.

---

## Quick reference

```
~/.agent/
├── dev-orchestrator/
│   ├── index.md              # active / paused / completed tasks
│   └── sessions/
│       └── <task-name>.md    # resumable session state
└── overlays/
    └── <repo-key>/
        └── SKILL.md          # repo-specific constraints
```
