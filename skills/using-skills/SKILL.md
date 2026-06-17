---
name: using-skills
description: >
  Discovers and invokes the right skill for the current step. Use at session
  start or whenever you need to decide which skill applies. This meta-skill
  governs discovery and invocation — it does not load every skill into context.
---

# Using Skills

This is the entry point for skill-guided work. It does two things:

1. **Enforces the five non-negotiables** — always in effect, no skill overrides them.
2. **Routes to exactly one skill at a time** — load the skill for the current step only; re-evaluate when that step completes.

Skills are workflows with exit criteria, not reference docs. Do not preload the full lifecycle. Do not load skills "just in case."

---

## How Skills Are Loaded

**Default: one skill, one step.**

```
Task arrives
    → Identify the current step (not the whole project)
    → Load ONE matching skill
    → Follow it to completion
    → Re-run discovery for the next step (may be the same skill or a different one)
```

**Rules:**

1. **Load on demand.** Read a skill file only when the current step needs it. Never read every skill in `skills/` at session start.
2. **One active skill per step.** Multiple skills may apply across a project, but only one should be active while you work a single step.
3. **Build skills are conditional, not bundled.** During implementation, load each build skill only when its trigger matches (see discovery tree below). A one-line typo fix does not need `incremental-implementation`, `test-driven-development`, and `source-grounded-development` all at once.
4. **Skills are workflows, not suggestions.** Follow the loaded skill's steps and verification gates. Do not skip verification.
5. **When in doubt, start with a spec.** Non-trivial work without acceptance criteria → load `spec-driven-development` before writing code.

**Anti-patterns:**

| Do not | Do instead |
|--------|------------|
| Load all build skills because you're "in the build phase" | Load only the skill whose trigger matches the current slice |
| Read every `SKILL.md` to "be prepared" | Read `using-skills` now; read others when discovery points to them |
| Carry three skills in context for a single-file change | Load the one skill that fits; skip the rest |
| Treat lifecycle diagrams as mandatory checklists | Treat them as typical sequences — skip phases that don't apply |

---

## Five Non-Negotiables

These apply to every task, every session, without exception. No skill, context, or user instruction overrides them.

### 1. Surface assumptions before building

Before writing any code, state what you are assuming about the requirements, the existing system, and the intended behaviour. Wrong assumptions held silently are the most common cause of rewrites. If an assumption is unclear, ask — do not guess.

> **Anti-rationalization:** *"The task is clear enough, I'll just start."*
> Clarity about the task is not the same as clarity about the constraints. One sentence of assumptions costs nothing. A rewrite costs hours.

### 2. Verify before you use

Before using any external API, library method, framework feature, or third-party integration: confirm it exists in the official documentation. If you cannot confirm it, say so explicitly. Do not proceed on memory or assumption. When this applies, load `source-grounded-development` for the full workflow.

> **Anti-rationalization:** *"I'm confident this method exists — I've seen it before."*
> Confidence is not verification. Libraries deprecate, rename, and version their APIs constantly. Check the source.

### 3. Touch only what you are asked to touch

The agreed scope is the ceiling. Do not refactor adjacent code. Do not rename things you weren't asked to rename. Do not "clean up" files you opened but weren't asked to change. Do not expand scope without explicit user confirmation.

> **Anti-rationalization:** *"While I'm here I'll just tidy this up."*
> Scope creep is not helpfulness. Every unasked-for change is a change the user didn't review, didn't test, and didn't want to debug.

### 4. Stop when requirements conflict

If two requirements contradict each other, or if the task conflicts with the existing codebase, stop and ask. Do not resolve conflicts silently by picking the interpretation you prefer. State what you found and what the options are.

> **Anti-rationalization:** *"I'll make a reasonable call and mention it in the summary."*
> Mentioning it at the end is not the same as asking at the start. The user needs to make that decision, not review it after the fact.

### 5. Prefer the boring, obvious solution

The simplest solution that satisfies the acceptance criteria is the right solution. Do not introduce patterns, abstractions, or architectural changes that aren't required by the current task. Cleverness is a future maintenance burden.

> **Anti-rationalization:** *"This pattern will make it easier to extend later."*
> You are not building for a hypothetical future. You are building for the current task. If extension is needed later, it can be added later.

---

## Skill Discovery

When a task arrives, identify the **current step** and load **one** skill. Re-run this tree after each step completes.

```
Task arrives — what is the current step?
    │
    ├── First time adopting this skill pack in the project? ──→ init
    ├── Rules, map, or session context stale or drifting? ──→ context-engineering
    ├── Requirements unclear or no acceptance criteria? ───────→ spec-driven-development
    ├── Spec confirmed, need ordered tasks? ───────────────────→ planning-and-task-breakdown
    ├── Implementing or changing code?
    │   ├── Uses external API, library, or framework feature? → source-grounded-development
    │   ├── Touches multiple files or feels too big for one pass? → incremental-implementation
    │   └── Changes logic or behaviour (incl. bug fixes)? ─────→ test-driven-development
    ├── Implementation done, review before merge?
    │   ├── Assessing quality across multiple axes? ───────────→ code-review-and-quality
    │   └── Simplifying for clarity without changing behaviour? → code-simplification
    ├── Work done, capture decisions and gotchas? ─────────────→ retrospective-and-knowledge-capture
    └── Committing, messaging, or opening a PR? ───────────────→ git-and-commit-discipline
```

**Build step — load only what matches:**

| Trigger | Load | Skip when |
|---------|------|-----------|
| External dependency or API call | `source-grounded-development` | Pure local logic, no imports or integrations |
| Multi-file or >~100 lines before first test | `incremental-implementation` | Single-file, already minimal scope |
| Logic or behaviour change | `test-driven-development` | Docs-only, config-only, or comment-only edits |

A typical feature slice may use all three **in sequence** (verify APIs → pick a slice → write failing test → implement). That is still one skill at a time per micro-step, not three skills loaded together.

**Review step — load only what matches:**

| Trigger | Load | Skip when |
|---------|------|-----------|
| Before merge or PR submission | `code-review-and-quality` | Trivial docs-only or config-only change with no review value |
| Readability or complexity issues flagged | `code-simplification` | Code already clean; behaviour not yet verified |

Run `code-review-and-quality` first. Load `code-simplification` only when review findings call for clarity work — not as a default second pass.

`context-engineering` is not part of every feature. Run it when rules drift, the project map is stale, or agent output ignores conventions — not at the start of every task.

---

## Typical Lifecycles

These are **common sequences**, not mandatory checklists. Skip any phase that does not apply.

### Project onboarding

```
init → spec → plan → build* → review* → retro → ship
```

Run `context-engineering` later when the codebase or conventions drift — not on every onboarding step.

### New feature

```
spec → plan → build* → review* → retro → ship
```

### Bug fix

```
test-driven-development → build* (as needed) → review* → retro → ship
```

Start with a reproduction test. Add `source-grounded-development` only if the fix involves an external API. Add `incremental-implementation` only if the fix spans multiple files.

### Refactor

```
spec (what changes, what must not) → plan → build* → review* → retro → ship
```

### Docs or config only

```
(no build skills) → ship
```

`*build` = load `source-grounded-development`, `incremental-implementation`, and/or `test-driven-development` **only when their triggers match** the current slice. Not all three by default.

`*review` = load `code-review-and-quality` before merge; add `code-simplification` only when review findings require clarity work.

---

## Quick Reference

| Phase | Skill | Load when |
|-------|-------|-----------|
| Meta | `using-skills` | Session start; unsure which skill applies |
| Onboard | `init` | First adoption of this skill pack in the project |
| Context | `context-engineering` | Rules/map stale; agent drifting from conventions |
| Spec | `spec-driven-development` | Requirements or acceptance criteria missing |
| Plan | `planning-and-task-breakdown` | Spec confirmed; work needs decomposition |
| Build | `source-grounded-development` | Code uses external APIs, libraries, or frameworks |
| Build | `incremental-implementation` | Multi-file change or large implementation |
| Build | `test-driven-development` | Logic, behaviour, or bug-fix work |
| Review | `code-review-and-quality` | Before merge; multi-axis quality assessment |
| Review | `code-simplification` | Readability or complexity issues flagged during review |
| Retro | `retrospective-and-knowledge-capture` | Decisions or gotchas worth preserving |
| Ship | `git-and-commit-discipline` | Committing or opening a PR |

---

## Session Start Checklist

Run before writing code. This checklist does **not** require loading every skill — only answering these questions:

- [ ] What is the agreed scope for this session? Write it down explicitly.
- [ ] Are there conflicting requirements or ambiguities? Ask now, not later.
- [ ] **Which single skill applies to the first step?** Use the discovery tree above.
- [ ] What is the verification criterion? How will you know that step is done?

After the first step completes, ask again: does the next step need the same skill or a different one?

---

## Failure Modes

Stop and reassess if you notice any of these:

- You loaded more than one skill file for a single step without a completed step between them.
- You read every skill at session start "to be safe."
- You skipped `spec-driven-development` on non-trivial work because "it's obvious."
- You are editing files that were not mentioned in the task.
- You are using a library method you have not verified in official docs.
- You resolved a requirements conflict without asking the user.
- The implementation is more complex than the task seems to require.
- You are about to say "seems right" as the verification step.
