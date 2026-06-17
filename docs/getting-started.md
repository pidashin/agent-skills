# Getting Started with Agent Skills

This repository provides a collection of production-grade engineering workflows (skills) for AI coding agents. Unlike simple guidelines or cheatsheets, these are **executable workflows with explicit triggers, processes, and verification gates**.

By loading these skills, your AI coding agent (e.g., Antigravity, Claude Code,
Gemini CLI, Cursor, or another Markdown-aware coding agent) is forced to follow
senior-engineer rigor: writing specs first, checking official APIs, breaking
down tasks, keeping commits atomic, and capturing domain knowledge at the end of
development.

---

## Skill Architecture

The skills are organized around the software development lifecycle:

1. **Meta (`using-skills`)** — Routes the agent to the correct skill and enforces five non-negotiable rules for every session.
2. **Orchestrator (`dev-orchestrator`, alias dev-flow)** — Multi-phase controller: isolated subagents, resumable task index and session state, maker/checker separation. Use for non-trivial work; use `using-skills` for single-step tasks.
3. **Init (`init`)** — One-time setup: level 1 rules files, domain knowledge store location, and GitHub CLI readiness.
4. **Context (`context-engineering`)** — Ongoing maintenance: keep rules files and project maps current; pack selective context per session.
5. **Spec (`spec-driven-development`)** — Defines acceptance criteria and scope boundaries before any code is written.
6. **Plan (`planning-and-task-breakdown`)** — Decomposes the spec into small, verifiable, and ordered tasks.
7. **Build** — Load `source-grounded-development`, `incremental-implementation`, and/or `test-driven-development` only when their triggers match the current slice (see `using-skills`).
8. **Review** — Load `code-review-and-quality` before merge; add `code-simplification` only when review findings require clarity work.
9. **Retro (`retrospective-and-knowledge-capture`)** — Records design patterns, trade-offs, and gotchas as domain knowledge.
10. **Ship (`git-and-commit-discipline`)** — Enforces atomic commits, conventional commit messages, and reviewable PRs.

Orchestrator state paths are tool-specific. See [dev-orchestrator-paths.md](./dev-orchestrator-paths.md).

---

## Standard Development Lifecycle

When working on a feature or a complex refactor, use `dev-orchestrator` (or `/dev-flow`).
The orchestrator advances through:

```
spec → plan → build → human-review → review → retro → ship
```

For single-step work, `using-skills` routes directly without orchestrator state:

```
spec → plan → build* → review* → retro → ship (git)
```

For a simple bug fix, the agent can skip spec/plan and proceed directly:

```
build* → review* → retro → ship (git)
```

---

## Adopting Skills in a Project

To use these skills in a new project, follow this onboarding workflow:

### 1. Initialize the Project Conventions
Run the project's onboarding process (e.g., using a project initialization script or `init` command) to establish key workspace conventions, including:
- Defining the **designated domain knowledge store** (e.g., a `docs/KNOWLEDGE.md` file, a wiki, or custom rules folder).
- Setting up the codebase-specific patterns and configurations.

If this repository is installed as a skill/plugin, invoke `/init` to run the
onboarding workflow. The command asks whether domain knowledge should be stored
locally, remotely, in a custom location, or skipped for now before it creates or
updates files.

After the project grows or agent output drifts from conventions, invoke `/context`
to audit rules files, update the project map, and pack context for the current task.

### 2. Install the plugin

Install **agent-skills** for your AI tool:

| Tool | Install |
|------|---------|
| **Cursor** | **Settings → Plugins → Add marketplace** → `https://github.com/pidashin/agent-skills` → install **agent-skills**. See [cursor-setup.md](./cursor-setup.md). |
| **Claude Code** | `/plugin marketplace add pidashin/agent-skills` then `/plugin install agent-skills@pidashin-agent-skills`. See [claude-setup.md](./claude-setup.md). |
| **Antigravity** | `agy plugin install https://github.com/pidashin/agent-skills.git`. See [antigravity-setup.md](./antigravity-setup.md). |
| **Gemini CLI** | Reference the `skills/` directory and use `.gemini/commands/` for command entrypoints. |

After installing, run **`/init`** once per project (see step 1 above).

#### Any Markdown-aware agent

Use `AGENTS.md` or the tool's equivalent instruction file to point the agent at
`skills/using-skills/SKILL.md`. The skills are plain Markdown workflows, so tools
without native skill support can still load them as rules or project context.

---

## The Five Non-Negotiables

These rules are loaded by `using-skills` and apply to **every session without exception**:

1. **Surface assumptions before building**: Never code in the dark. State constraints first.
2. **Verify before you use**: Check official docs for package APIs. Never guess or rely on memory.
3. **Touch only what you are asked to touch**: Absolute scope discipline. No opportunistic refactoring.
4. **Stop when requirements conflict**: Do not pick a side silently. Stop and ask the user.
5. **Prefer the boring, obvious solution**: Write simple, readable, and standard code. Avoid premature abstraction.
