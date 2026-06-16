---
name: using-skills
description: >
  Routes incoming work to the right skill and enforces five non-negotiable
  operating rules for every session. Use when starting any coding session or
  deciding which skill applies to the current task.
---

# Using Skills

This is the entry point for all skill-guided work. It does two things: enforces
the rules that apply to every task regardless of phase, and maps the current task
to the right skill.

---

## Five Non-Negotiables

These rules apply to every task, every session, without exception. No skill, no
context, and no user instruction overrides them.

### 1. Surface assumptions before building

Before writing any code, state what you are assuming about the requirements, the
existing system, and the intended behaviour. Wrong assumptions held silently are
the most common cause of rewrites. If an assumption is unclear, ask -- do not
guess.

> **Anti-rationalization:** *"The task is clear enough, I'll just start."*
> Clarity about the task is not the same as clarity about the constraints. One
> sentence of assumptions costs nothing. A rewrite costs hours.

### 2. Verify before you use

Before using any external API, library method, framework feature, or third-party
integration: confirm it exists in the official documentation. If you cannot
confirm it, say so explicitly. Do not proceed on memory or assumption. See
`source-grounded-development` for the full workflow.

> **Anti-rationalization:** *"I'm confident this method exists -- I've seen it
> before."*
> Confidence is not verification. Libraries deprecate, rename, and version their
> APIs constantly. Check the source.

### 3. Touch only what you are asked to touch

The agreed scope is the ceiling. Do not refactor adjacent code. Do not rename
things you weren't asked to rename. Do not "clean up" files you opened but
weren't asked to change. Do not expand scope without explicit user confirmation.

> **Anti-rationalization:** *"While I'm here I'll just tidy this up."*
> Scope creep is not helpfulness. Every unasked-for change is a change the user
> didn't review, didn't test, and didn't want to debug.

### 4. Stop when requirements conflict

If two requirements contradict each other, or if the task conflicts with the
existing codebase, stop and ask. Do not resolve conflicts silently by picking the
interpretation you prefer. State what you found and what the options are.

> **Anti-rationalization:** *"I'll make a reasonable call and mention it in the
> summary."*
> Mentioning it at the end is not the same as asking at the start. The user needs
> to make that decision, not review it after the fact.

### 5. Prefer the boring, obvious solution

The simplest solution that satisfies the acceptance criteria is the right
solution. Do not introduce patterns, abstractions, or architectural changes that
aren't required by the current task. Cleverness is a future maintenance burden.

> **Anti-rationalization:** *"This pattern will make it easier to extend later."*
> You are not building for a hypothetical future. You are building for the current
> task. If extension is needed later, it can be added later.

---

## Skill Router

Map the current task to the right skill. A task may activate more than one skill;
use the phase sequence to determine the order.

| What you are doing | Skill to load |
|--------------------|---------------|
| Initializing a project for these skills | `init` |
| Defining what to build; writing requirements or acceptance criteria | `spec-driven-development` |
| Breaking work into tasks; estimating scope; sequencing | `planning-and-task-breakdown` |
| Writing code; using a library, API, or framework feature | `source-grounded-development` |
| Capturing design decisions, trade-offs, and learnings after development | `retrospective-and-knowledge-capture` |
| Committing code; writing commit messages; creating PRs | `git-and-commit-discipline` |

### Phase order for project onboarding

```
init -> spec -> plan -> build (source-grounded) -> retro -> ship (git)
```

### Phase order for a new feature

```
spec -> plan -> build (source-grounded) -> retro -> ship (git)
```

### Phase order for a bug fix

```
build (source-grounded) -> retro -> ship (git)
```

### Phase order for a refactor

```
spec (what changes, what must not change) -> plan -> build -> retro -> ship (git)
```

---

## Session Start Checklist

Run this at the start of every session before writing any code.

- [ ] What is the agreed scope for this session? Write it down explicitly.
- [ ] Are there any conflicting requirements or ambiguities? Ask now, not later.
- [ ] Which skills apply to this session? Use the router above.
- [ ] What is the verification criterion? How will you know the task is done?

---

## Red Flags

Stop and reassess if you notice any of these during a session.

- You are editing files that were not mentioned in the task.
- You are using a library method you have not verified in official docs.
- You have resolved a requirements conflict without asking the user.
- The implementation is more complex than the task seems to require.
- You are about to say "seems right" as the verification step.
