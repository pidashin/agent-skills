---
name: dev-orchestrator
description: >
  Central orchestrator for multi-phase development. Coordinates isolated subagent
  workers across spec, plan, build, review, retro, and ship. Maintains resumable
  task index and session state. Enforces maker/checker separation. Also invoked as
  dev-flow. Use for non-trivial features, bugs, or refactors that span multiple
  phases. Use using-skills for single-step work.
---

# Dev Orchestrator

Alias: **dev-flow**

The main session is the orchestrator: it coordinates isolated subagent workers,
keeps resumable session notes, gates implementation before review, enforces
maker/checker separation, and hands back only after checker verification.

Worker playbooks come from the lifecycle skills in this repo (`spec-driven-development`,
`planning-and-task-breakdown`, build skills, `code-review-and-quality`, etc.).
Load **one worker skill per subagent step** — the orchestrator does not replace them.

---

## When to Use

Use dev-orchestrator when:

- Work spans multiple lifecycle phases (spec through ship).
- Subagent isolation or maker/checker separation is needed.
- You may pause and resume later from the same point.
- The user invokes `/dev-orchestrator`, `/dev-flow`, or asks to continue a task.

Use `using-skills` instead when:

- The task is a single step (one file, docs-only, trivial fix).
- No session state or subagent delegation is needed.

---

## Phase Model

The orchestrator state machine:

```
spec → plan → build → human-review → review → retro → ship
```

Skip phases that do not apply. Common shortcuts:

| Work type | Typical path |
|-----------|--------------|
| New feature | spec → plan → build → human-review → review → retro → ship |
| Bug fix | spec (minimal repro AC) → build → human-review → review → retro → ship |
| Refactor | spec → plan → build → human-review → review → retro → ship |
| Docs / config only | ship (or spec → ship when scope is unclear) |

| Phase | Orchestrator | Worker skill (one per subagent step) |
|-------|--------------|----------------------------------------|
| spec | Intake, classify (`feature`, `bug`, `mixed`, `unknown`); context subagent if needed | `spec-driven-development` |
| plan | Task breakdown in session state | `planning-and-task-breakdown` |
| build | Isolated **maker** subagent as sole code writer | `source-grounded-development`, `incremental-implementation`, and/or `test-driven-development` per trigger |
| human-review | Present diff to user; wait for approval before review | none (user gate) |
| review | Isolated **checker** subagent, readonly | `code-review-and-quality`; `code-simplification` only when review finds clarity issues |
| retro | Capture decisions and gotchas | `retrospective-and-knowledge-capture` |
| ship | Commit and PR hygiene | `git-and-commit-discipline` |

The five non-negotiables from `AGENTS.md` apply to every phase without exception.

---

## Repo Overlay

After detecting the current repo, load a project overlay when present.

**Path resolution:** read [`docs/dev-orchestrator-paths.md`](../../docs/dev-orchestrator-paths.md)
for tool-specific locations. Use these **logical paths**:

| Logical path | Purpose |
|--------------|---------|
| `<user-overlays-root>/overlays/<repo-key>/SKILL.md` | User-local overlay |
| `.agent/overlays/<repo-key>/SKILL.md` | Repo-local overlay (team-shared, optional) |

**Load order:** repo-local first, then user-local. If neither exists, run the generic orchestrator only.

**Default** (when the paths doc does not specify a tool override): agent state and
overlays live under `~/.agent/` (see paths doc).

Detection order for repo key (first match wins):

1. Explicit repo key in the user's message.
2. Git remote slug (e.g. `houzz/visual-tools` → `visual-tools`).
3. Workspace folder name.

Overlays add repo-specific constraints (commands, paths, verification, specialist
roles). They do **not** replace the phase model above.

---

## State and Resume

### Path resolution

Runtime state is user-local (not committed to git). Resolve concrete paths using
[`docs/dev-orchestrator-paths.md`](../../docs/dev-orchestrator-paths.md) for the
active AI tool.

**Logical paths** (same meaning on every tool):

| Artifact | Logical path |
|----------|--------------|
| Task index | `<agent-state-root>/dev-orchestrator/index.md` |
| Session state | `<agent-state-root>/dev-orchestrator/sessions/<task-name>.md` |

**Canonical default** when no tool-specific mapping applies: `<agent-state-root>` is `~/.agent/`.

Create directories if missing. Templates: `skills/dev-orchestrator/templates/`.

The orchestrator session **owns writes** to the index and session files. Subagents
may propose updates; the orchestrator is the writer of record.

### Task index

Update the index when starting, resuming, pausing, handing off, or completing a task.
Use the format in `templates/index.md`. Include a **Repo** column on every row.

### Session state

Before substantial work, create or resume a session file using `templates/session.md`.
Use a short kebab-case name from the task unless the user provides one.

Update after intake, context gathering, subagent findings, each phase transition,
implementation, verification, review feedback, blockers, and before stopping.

### Resume

When the user says **continue** `<task-name>` (via `/dev-orchestrator` or natural language):

1. Read the task index at the resolved path.
2. Read the task's session file.
3. Confirm `Current Phase`, active subtasks, and `Exact Next Action`.
4. If the workspace repo matches the task's repo, inspect git status / focused diff.
5. Update the index timestamp and continue — do not restart from spec unless state is stale or missing.

If the task name is ambiguous, list matching active/paused tasks and ask the user to choose.

### New task

When starting fresh (no continue intent):

1. Classify the request.
2. Detect repo and load overlay if present.
3. Create session file and index row with phase `spec` (or the appropriate starting phase).
4. Proceed with the current phase.

---

## Core Contract

The user provides the requirement and verifies the final output. The orchestrator owns the middle:

1. Intake and classify the request.
2. Ask early for high-value missing inputs (overlay may specify what to ask).
3. Create or resume task index entry and session state.
4. Dispatch context-gathering subagents and load repo overlay constraints.
5. Advance phase by phase; dispatch worker subagents with scoped briefs.
6. Dispatch an isolated **maker** subagent as the single code writer by default.
7. Stop at **human-review** before checker work; present diff and wait for user approval.
8. After approval, dispatch **checker** subagents (different session from maker) for review and verification.
9. Route blocking feedback back to a maker subagent; re-check until clear.
10. Final handoff with evidence, which subagents ran, remaining risks, and session path.

---

## Human Review Gate

Before starting review subagents, broad verification, or review loops, stop at
`human-review` and show the user:

- what changed
- focused diff / files to review
- lightweight author checks already run, if any
- what checker work is queued next
- the phrase that starts review (e.g. "ready for review")

Lightweight author checks by the maker are allowed before this gate when cheap
(targeted test, type check). Defer expensive verification until the user approves direction.

When the user gives feedback at this gate:

- Record feedback in session state; set `Current Phase` back to `build`
- Classify as correction, behavior change, scope change, or clarification
- Ask only if feedback changes product behavior, data semantics, or scope
- Dispatch maker subagent to apply feedback
- Return to `human-review` with a delta summary before review

Do not start review just because build appears complete. Review starts only after
explicit user approval or a clear message that implementation looks good.

---

## Maker / Checker Separation

For every code-changing subtask:

- Record **maker** and **checker** separately in the session subtasks table.
- The **maker** is the subagent that edited code or generated the patch.
- The **checker** is a **different subagent session** that did not edit that code.
- A maker may report author checks; those do not count as acceptance review.
- Review, QA, and requirement coverage must be done by checker subagents.
- If a checker finds blocking issues, route fixes to a maker, then re-check.
- The maker must never become the checker for the same subtask.
- If no distinct checker can be identified, stop and ask the user.

Checker subagent brief should include: acceptance criteria, diff / changed files,
test output. It must **not** include the maker's implementation conversation or rationale.

---

## Subagent Rules

- The orchestrator coordinates; it is not the default code writer, test runner, or reviewer.
- Only one maker subagent edits a given code slice by default.
- Multiple subagents may investigate or review in parallel; writing stays single-owner.
- Do not run parallel makers unless tasks are proven independent with no file overlap.
- Give each subagent a focused, self-contained prompt: scope, constraints, expected output.
- Synthesize investigation findings into session state before dispatching build.
- If subagents disagree on direction, stop and ask the user.

Expected subagent output format:

```markdown
## Findings
## Recommended Constraints
## Risks
## Likely Files / Symbols
## Verification Suggestions
```

Use filesystem isolation (worktree or `best-of-n-runner`) when experiments or
parallel attempts need separate trees.

---

## Local Context

Before plan or build, dispatch a context subagent to read repo guidance and report
constraints back. Minimum for any repo:

- `AGENTS.md` / `CLAUDE.md` (or tool-specific entrypoints)
- Relevant rules and skills the overlay or task references

The overlay specifies additional paths (e.g. `.agent/skills/`, repo-local build or
verification skills).

---

## Final Handoff

Include:

- what changed
- how it was verified
- which subagents / reviews ran (maker vs checker)
- remaining risks or manual checks
- session state path (resolved logical path to the session file)

Move completed tasks to the **Completed Tasks** section of the index with a one-line summary.

---

## Task Dashboard (`dev-flow-tasks`)

When the user invokes `/dev-flow-tasks` (or asks what is in progress):

1. Resolve the task index path per [`docs/dev-orchestrator-paths.md`](../../docs/dev-orchestrator-paths.md).
2. If missing, report that no dev-flow tasks exist yet; suggest `/dev-orchestrator` or `/dev-flow` to start one.
3. For each **active** and **paused / blocked** task, read the linked session file and summarize:
   - task name, repo, status, current phase, exact next action
   - open blockers and latest progress log entry
   - subtask maker/checker status when build or review is in progress
4. Do not list completed tasks unless the user asks.
5. When the workspace repo matches a task's repo column, note git branch/status in one line.

Present as a compact dashboard the user can scan to decide what to continue.

---

## Stop Conditions

Stop and ask the user when:

- product behavior is ambiguous enough to change UX or data semantics
- spec inputs conflict (e.g. PRD vs design)
- root cause cannot be established and no plausible path emerges
- cross-repo or backend scope is required unexpectedly
- verification is blocked (auth, manual steps, missing env)
- a destructive git action would be needed
- subagents disagree on direction
- verification fails and the next step is not obvious

---

## Relationship to Other Skills

| Skill | Role |
|-------|------|
| `dev-orchestrator` | Multi-phase CPU: spawn, state, gates, maker/checker |
| `using-skills` | Single-step router when orchestrator is overkill |
| Lifecycle skills | Worker playbooks dispatched per phase |
| Repo overlay | Project-specific constraints on top of generic phases |
