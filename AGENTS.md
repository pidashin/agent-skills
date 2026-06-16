# AGENTS.md

This file is read automatically by AI coding agents (Claude Code, Cursor, Windsurf, etc.) at the start of every session. It contains the five non-negotiables that apply to all work in this repo, regardless of the task.

For the full skill workflows, see [skills/using-skills/SKILL.md](./skills/using-skills/SKILL.md).

---

## Five non-negotiables

### 1. Surface assumptions before building

Before writing any code, state what you are assuming about the requirements, the existing system, and the intended behaviour. If an assumption is unclear, ask — do not guess.

### 2. Verify before you use

Before using any external API, library method, framework feature, or third-party integration: confirm it exists in the official documentation. If you cannot confirm it, say so explicitly. Do not proceed on memory or assumption.

### 3. Touch only what you are asked to touch

The agreed scope is the ceiling. Do not refactor adjacent code. Do not rename things you weren't asked to rename. Do not expand scope without explicit confirmation.

### 4. Stop when requirements conflict

If two requirements contradict each other, or if the task conflicts with the existing codebase, stop and ask. Do not resolve conflicts silently by picking the interpretation you prefer.

### 5. Prefer the boring, obvious solution

The simplest solution that satisfies the acceptance criteria is the right solution. Do not introduce patterns or abstractions that aren't required by the current task.

---

## Skills

Load the appropriate skill for the current phase:

| Phase | Skill |
|-------|-------|
| Always | [using-skills](./skills/using-skills/SKILL.md) |
| Initializing a project | [init](./skills/init/SKILL.md) |
| Defining what to build | [spec-driven-development](./skills/spec-driven-development/SKILL.md) |
| Breaking work into tasks | [planning-and-task-breakdown](./skills/planning-and-task-breakdown/SKILL.md) |
| Writing code with external dependencies | [source-grounded-development](./skills/source-grounded-development/SKILL.md) |
| Capturing design decisions and retrospecting | [retrospective-and-knowledge-capture](./skills/retrospective-and-knowledge-capture/SKILL.md) |
| Committing or opening a PR | [git-and-commit-discipline](./skills/git-and-commit-discipline/SKILL.md) |
