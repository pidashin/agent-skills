# Using agent-skills with Claude Code

Claude Code discovers skills and slash commands from installed plugins. Installing
**agent-skills** gives the agent structured workflows, lifecycle entrypoints, and
verification gates without loading every skill into context at session start.

---

## Installation

### Add marketplace (recommended)

1. In a Claude Code session, add the marketplace:
   ```text
   /plugin marketplace add pidashin/agent-skills
   ```
2. Install the plugin (pick user, project, or local scope when prompted):
   ```text
   /plugin install agent-skills@pidashin-agent-skills
   ```
3. On each project, run `/init` once to create `AGENTS.md` / `CLAUDE.md` and project rules.

You can also use the plugin browser: run `/plugin`, open the **Discover** tab, and
install from the **pidashin-agent-skills** marketplace.

To pull in updates after a new release:

```text
/plugin marketplace update pidashin/agent-skills
```

### From a local clone

If you have cloned the repository locally (for example to customize skills):

```text
/plugin marketplace add /path/to/agent-skills
/plugin install agent-skills@pidashin-agent-skills
```

Or from the shell:

```bash
claude plugin marketplace add /path/to/agent-skills
claude plugin install agent-skills@pidashin-agent-skills
```

### Troubleshooting

If install fails with a Git permission error, Claude Code may be cloning via SSH
without an available key. Force HTTPS for GitHub:

```bash
git config --global url."https://github.com/".insteadOf git@github.com:
```

Then retry `/plugin install agent-skills@pidashin-agent-skills`.

---

## After install

1. Run `/init` on a new project to set up level 1 rules files and the domain
   knowledge store location.
2. Confirm slash commands work: `/spec`, `/planning`, `/build`, `/review`, `/ship`.
3. Run `/context` when rules or project conventions drift.

---

## How it works

Claude Code uses **progressive disclosure** — the same pattern this repository is
built around.

### Session start

1. Claude reads project instruction files (`CLAUDE.md`, `AGENTS.md`).
2. Skill **names and descriptions** from the plugin are available to the agent.
3. The agent loads **one** full skill when the current step needs it.

Run the **Session Start Checklist** from `using-skills` before writing code.

### Lifecycle

```
spec → plan → build* → review* → retro → ship (git)
```

For non-trivial work, use `/dev-flow` or `/dev-orchestrator` instead.

Orchestrator state paths are tool-specific. See [dev-orchestrator-paths.md](./dev-orchestrator-paths.md).

---

## Slash commands

Commands ship in [`.claude/commands/`](../.claude/commands/):

| Command | Skill |
|---------|-------|
| `/init` | `init` |
| `/context` | `context-engineering` |
| `/spec` | `spec-driven-development` |
| `/planning` | `planning-and-task-breakdown` |
| `/build` | Build skills as triggered (see `using-skills`) |
| `/review` | Review skills as triggered (see `using-skills`) |
| `/retro` | `retrospective-and-knowledge-capture` |
| `/ship` | `git-and-commit-discipline` |
| `/dev-orchestrator`, `/dev-flow` | `dev-orchestrator` |
| `/dev-flow-tasks` | `dev-orchestrator` (task dashboard) |

---

## Further reading

- [Getting Started](./getting-started.md) — skill architecture and adoption workflow
- [Claude Code: Discover plugins](https://code.claude.com/docs/en/discover-plugins)
- [Claude Code: Plugin marketplaces](https://code.claude.com/docs/en/plugin-marketplaces)
