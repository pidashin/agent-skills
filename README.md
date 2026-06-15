# agent-skills

Production-grade skills for AI coding agents — focused on scope discipline, verified APIs, and shipping code that a human can actually review.

Built on the [agent-skills pattern](https://addyosmani.com/blog/agent-skills/) by Addy Osmani. This is my own set, tuned for full-stack TypeScript/JavaScript work and the things I actually need AI agents to get right.

---

## The problem this solves

AI coding agents default to the shortest path. That path skips specs. It uses APIs from memory rather than documentation. It refactors things it wasn't asked to touch. It declares things done without evidence.

These skills bolt the senior-engineer scaffolding back on. They are not reference docs — they are **workflows with exit criteria**. The agent follows them; you verify the result.

---

## Skills

7 skills organised around the development lifecycle.

### Meta
| Skill | Phase | What it does |
|-------|-------|--------------|
| [using-skills](./skills/using-skills/SKILL.md) | Always | Routes to the right skill; enforces five non-negotiables for every session |

### Lifecycle
| Skill | Phase | What it does |
|-------|-------|--------------|
| [spec-driven-development](./skills/spec-driven-development/SKILL.md) | Spec | Write acceptance criteria before writing any code |
| [planning-and-task-breakdown](./skills/planning-and-task-breakdown/SKILL.md) | Plan | Decompose the spec into atomic, verifiable tasks with explicit scope boundaries |
| [source-grounded-development](./skills/source-grounded-development/SKILL.md) | Build | Verify every API, method, and library feature in official docs before using it |
| [git-and-commit-discipline](./skills/git-and-commit-discipline/SKILL.md) | Ship | Atomic commits, meaningful messages, reviewable PRs |

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

---

## Usage

### Claude Code

Drop the `skills/` directory into your project or reference it in your `CLAUDE.md`:

```
Read and follow the skills in ./skills/ for all coding tasks.
Start with ./skills/using-skills/SKILL.md.
```

### Cursor

Copy skills into `.cursor/rules/` or reference them in your project rules.

### Any agent

These are plain Markdown files. Any agent that accepts system prompts or instruction files can use them. Point the agent at `skills/using-skills/SKILL.md` to start.

---

## AGENTS.md

See [AGENTS.md](./AGENTS.md) for the five non-negotiables in a format suitable for any agent that reads it automatically.

---

## License

MIT
