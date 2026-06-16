# Getting Started with Agent Skills

This repository provides a collection of production-grade engineering workflows (skills) for AI coding agents. Unlike simple guidelines or cheatsheets, these are **executable workflows with explicit triggers, processes, and verification gates**.

By loading these skills, your AI coding agent (e.g., Antigravity, Claude Code, Cursor) is forced to follow senior-engineer rigor: writing specs first, checking official APIs, breaking down tasks, keeping commits atomic, and capturing domain knowledge at the end of development.

---

## Skill Architecture

The skills are organized around the software development lifecycle:

1. **Meta (`using-skills`)** — Routes the agent to the correct skill and enforces five non-negotiable rules for every session.
2. **Spec (`spec-driven-development`)** — Defines acceptance criteria and scope boundaries before any code is written.
3. **Plan (`planning-and-task-breakdown`)** — Decomposes the spec into small, verifiable, and ordered tasks.
4. **Build (`source-grounded-development`)** — Verifies third-party APIs and libraries in official docs before coding.
5. **Retro (`retrospective-and-knowledge-capture`)** — Records design patterns, trade-offs, and gotchas as domain knowledge.
6. **Ship (`git-and-commit-discipline`)** — Enforces atomic commits, conventional commit messages, and reviewable PRs.

---

## Standard Development Lifecycle

When working on a feature or a complex refactor, the agent will transition through the following phases:

```
spec → plan → build (source-grounded) → retro → ship (git)
```

For a simple bug fix, the agent can skip spec/plan and proceed directly:

```
build (source-grounded) → retro → ship (git)
```

---

## Adopting Skills in a Project

To use these skills in a new project, follow this onboarding workflow:

### 1. Initialize the Project Conventions
Run the project's onboarding process (e.g., using a project initialization script or `init` command) to establish key workspace conventions, including:
- Defining the **designated domain knowledge store** (e.g., a `docs/KNOWLEDGE.md` file, a wiki, or custom rules folder).
- Setting up the codebase-specific patterns and configurations.

### 2. Configure Your AI Coding Tool

#### Antigravity CLI (`agy`)
Install this repository as a native plugin to let the CLI automatically load, enforce, and interact with these skills:
```bash
agy plugin install https://github.com/pidashin/agent-skills.git
```
*(For more details, see [antigravity-setup.md](./antigravity-setup.md)).*

#### Claude Code
Add the following instruction to your `CLAUDE.md` file at the root of your project:
```
Read and follow the skills in ./skills/ for all coding tasks.
Start with ./skills/using-skills/SKILL.md to determine the active skill.
```

#### Cursor
Copy the skills directory (or specific rules) into `.cursor/rules/` or reference them in your `.cursorrules` configuration file to enforce compliance.

---

## The Five Non-Negotiables

These rules are loaded by `using-skills` and apply to **every session without exception**:

1. **Surface assumptions before building**: Never code in the dark. State constraints first.
2. **Verify before you use**: Check official docs for package APIs. Never guess or rely on memory.
3. **Touch only what you are asked to touch**: Absolute scope discipline. No opportunistic refactoring.
4. **Stop when requirements conflict**: Do not pick a side silently. Stop and ask the user.
5. **Prefer the boring, obvious solution**: Write simple, readable, and standard code. Avoid premature abstraction.
