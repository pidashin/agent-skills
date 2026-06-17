# agent-skills

Production-grade skills for AI coding agents — focused on scope discipline, verified APIs, and shipping code that a human can actually review.

Built on the [agent-skills pattern](https://addyosmani.com/blog/agent-skills/) by Addy Osmani. This is my own set, tuned for full-stack TypeScript/JavaScript work and the things I actually need AI agents to get right.

---

## The problem this solves

AI coding agents default to the shortest path. That path skips specs. It uses APIs from memory rather than documentation. It refactors things it wasn't asked to touch. It declares things done without evidence.

These skills bolt the senior-engineer scaffolding back on. They are not reference docs — they are **workflows with exit criteria**. The agent follows them; you verify the result.

---

## Skills

13 skills organised around onboarding, context maintenance, orchestration, and the development lifecycle.

### Meta and onboarding
| Skill | Phase | What it does |
|-------|-------|--------------|
| [using-skills](./skills/using-skills/SKILL.md) | Always | Routes to the right skill; enforces five non-negotiables for every session |
| [dev-orchestrator](./skills/dev-orchestrator/SKILL.md) | Orchestrator | Multi-phase controller with subagents, resumable state, maker/checker separation (alias dev-flow) |
| [init](./skills/init/SKILL.md) | Onboarding | One-time setup: level 1 rules files, domain knowledge store location, GitHub CLI readiness |
| [context-engineering](./skills/context-engineering/SKILL.md) | Context | Keeps rules files and project maps current; packs selective context per session |

### Lifecycle
| Skill | Phase | What it does |
|-------|-------|--------------|
| [spec-driven-development](./skills/spec-driven-development/SKILL.md) | Spec | Write acceptance criteria before writing any code |
| [planning-and-task-breakdown](./skills/planning-and-task-breakdown/SKILL.md) | Plan | Decompose the spec into atomic, verifiable tasks with explicit scope boundaries |
| [source-grounded-development](./skills/source-grounded-development/SKILL.md) | Build | Verify every API, method, and library feature in official docs before using it |
| [incremental-implementation](./skills/incremental-implementation/SKILL.md) | Build | Ship multi-file work in thin vertical slices; test and commit each increment |
| [test-driven-development](./skills/test-driven-development/SKILL.md) | Build | Write failing tests first; reproduce bugs before fixing them |
| [code-review-and-quality](./skills/code-review-and-quality/SKILL.md) | Review | Multi-axis review across correctness, readability, architecture, security, and performance |
| [code-simplification](./skills/code-simplification/SKILL.md) | Review | Reduce complexity while preserving exact behaviour |
| [retrospective-and-knowledge-capture](./skills/retrospective-and-knowledge-capture/SKILL.md) | Retro | Document design patterns, rationales, and codebase gotchas |
| [git-and-commit-discipline](./skills/git-and-commit-discipline/SKILL.md) | Ship | Atomic commits, meaningful messages, reviewable PRs |

---

## Commands

Slash commands are thin entrypoints that invoke the matching skill. They live
outside `skills/` so each AI tool can use its own command format while sharing
the same skill workflow.

| Command | Purpose | Skill |
|---------|---------|-------|
| `/dev-orchestrator` | Start or continue multi-phase work (new task or continue) | [dev-orchestrator](./skills/dev-orchestrator/SKILL.md) |
| `/dev-flow` | Alias for `/dev-orchestrator` | [dev-orchestrator](./skills/dev-orchestrator/SKILL.md) |
| `/dev-flow-tasks` | List in-progress tasks with phase, next action, and blockers | [dev-orchestrator](./skills/dev-orchestrator/SKILL.md) |

Orchestrator state and overlay paths vary by AI tool. See [docs/dev-orchestrator-paths.md](./docs/dev-orchestrator-paths.md).

| `/init` | One-time setup: level 1 rules files and domain knowledge store | [init](./skills/init/SKILL.md) |
| `/context` | Audit and refresh rules files, project map, and session context | [context-engineering](./skills/context-engineering/SKILL.md) |
| `/spec` | Define acceptance criteria and scope before code | [spec-driven-development](./skills/spec-driven-development/SKILL.md) |
| `/planning` | Break a confirmed spec into verifiable tasks | [planning-and-task-breakdown](./skills/planning-and-task-breakdown/SKILL.md) |
| `/build` | Implement the current slice; load build skills only when their triggers match | See [using-skills](./skills/using-skills/SKILL.md) |
| `/review` | Review the change before merge; load review skills only when their triggers match | See [using-skills](./skills/using-skills/SKILL.md) |
| `/retro` | Capture design decisions and codebase gotchas | [retrospective-and-knowledge-capture](./skills/retrospective-and-knowledge-capture/SKILL.md) |
| `/ship` | Prepare atomic commits and reviewable shipping notes | [git-and-commit-discipline](./skills/git-and-commit-discipline/SKILL.md) |

Tool-specific command locations:

- Claude Code: [`.claude/commands/`](./.claude/commands/)
- Gemini CLI: [`.gemini/commands/`](./.gemini/commands/)
- Antigravity CLI: [`commands/`](./commands/)
- Cursor: plugin via [`.cursor-plugin/`](./.cursor-plugin/) (see [cursor-setup.md](./docs/cursor-setup.md))

---

## Five non-negotiables

These apply to every session, regardless of which skill is active. They live in `using-skills` and are never optional.

1. **Surface assumptions before building.** State what you're assuming; wrong assumptions held silently cause rewrites.
2. **Verify before you use.** Check every API and library method against official docs. Do not proceed on memory.
3. **Touch only what you're asked to touch.** No opportunistic refactors. No scope creep. No "while I'm here" changes.
4. **Stop when requirements conflict.** Don't resolve ambiguity silently. Ask.
5. **Prefer the boring, obvious solution.** Cleverness is a future maintenance burden.

---

## Design principles

These skills follow the principles from Addy Osmani's agent-skills project:

- **Process over prose.** Every skill is a workflow with steps and exit criteria — not an essay about best practices.
- **Anti-rationalization tables.** Every skill includes pre-written rebuttals to the excuses agents use to skip steps.
- **Verification is non-negotiable.** Every skill ends with concrete evidence requirements. "Seems right" never closes the loop.
- **Progressive disclosure.** Load the meta-skill first; it routes to the right skill for the current phase.
- **Scope discipline.** The most important principle: touch only what was agreed. Scope creep is not helpfulness.
- **Incremental, test-driven build.** During implementation, verify APIs first, ship thin vertical slices, and prove each change with tests. Adapted from [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills).

---

## Installation

Install **agent-skills** from GitHub for your AI tool:

| Tool | Install steps | Guide |
|------|---------------|-------|
| **Cursor** | **Settings → Plugins → Add marketplace** → `https://github.com/pidashin/agent-skills` → install **agent-skills** | [cursor-setup.md](./docs/cursor-setup.md) |
| **Claude Code** | `/plugin marketplace add pidashin/agent-skills` then `/plugin install agent-skills@pidashin-agent-skills` | [claude-setup.md](./docs/claude-setup.md) |
| **Antigravity** | `agy plugin install https://github.com/pidashin/agent-skills.git` | [antigravity-setup.md](./docs/antigravity-setup.md) |

After installing on any tool, run **`/init`** once per project to set up `AGENTS.md`
and project-specific rules.

### Gemini CLI

Install or reference the [`skills/`](./skills/) directory, and use
[`.gemini/commands/`](./.gemini/commands/) for Gemini command entrypoints.

### Any agent

These are plain Markdown files. Any agent that accepts system prompts or instruction
files can use them. Point the agent at `skills/using-skills/SKILL.md` to start.

---

## Documentation

- [Getting Started](./docs/getting-started.md) — skill architecture and lifecycle
- [Claude Setup](./docs/claude-setup.md)
- [Cursor Setup](./docs/cursor-setup.md)
- [Antigravity Setup](./docs/antigravity-setup.md)
- [Dev-orchestrator paths](./docs/dev-orchestrator-paths.md) — tool-specific state locations

---

## Usage

For detailed integration, verification, and project rules setup, see the guides above.

### Claude Code (manual fallback)

If you cannot use the plugin marketplace, drop `skills/` into your project and add
to `CLAUDE.md`:

```
Read and follow the skills in ./skills/ for all coding tasks.
Start with ./skills/using-skills/SKILL.md.
```

---

## Project Structure

```text
agent-skills/
├── skills/                 # Shared skill workflows
│   ├── using-skills/       # Meta skill
│   ├── dev-orchestrator/   # Multi-phase orchestrator (alias dev-flow)
│   ├── init/               # Project onboarding
│   ├── context-engineering/  # Ongoing context maintenance
│   └── ...
├── .claude/commands/       # Claude Code slash commands (also used by Cursor plugin)
├── .claude-plugin/         # Claude plugin manifest + custom marketplace index
├── .cursor-plugin/         # Cursor plugin manifest + custom marketplace index
├── .gemini/commands/       # Gemini CLI slash commands
├── commands/               # Antigravity CLI slash commands
├── plugin.json             # Antigravity plugin manifest
└── docs/                   # Setup guides and tool-specific path mappings
    ├── getting-started.md
    ├── claude-setup.md
    ├── cursor-setup.md
    ├── antigravity-setup.md
    └── dev-orchestrator-paths.md
```

---

## AGENTS.md

See [AGENTS.md](./AGENTS.md) for the five non-negotiables in a format suitable for any agent that reads it automatically.

---

## License

MIT
