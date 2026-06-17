# Using agent-skills with Cursor

Cursor discovers skills automatically and loads them on demand. Installing this
repository gives the agent structured workflows, lifecycle entrypoints, and
verification gates without stuffing every skill into context at session start.

---

## Installation

### From GitHub with `gh skill` (Recommended)

Install all skills for Cursor with the GitHub CLI:

```bash
gh skill install pidashin/agent-skills --all --agent cursor
```

**User scope** (available in every project on your machine):

```bash
gh skill install pidashin/agent-skills --all --agent cursor --scope user
```

**Project scope** (committed with the repo, shared with teammates):

```bash
gh skill install pidashin/agent-skills --all --agent cursor --scope project
```

To inspect before installing:

```bash
gh skill preview pidashin/agent-skills
```

`gh skill` is a preview GitHub CLI feature. Confirm it exists in your local `gh`
version before depending on it. At project scope, Cursor installs skills into
`.cursor/skills/` (or the shared `.agents/skills/` directory).

### From a Local Clone

If you are developing or customizing the skills locally:

1. Clone the repository:
   ```bash
   git clone https://github.com/pidashin/agent-skills.git
   ```
2. Install from the local path:
   ```bash
   gh skill install /path/to/agent-skills --all --agent cursor --from-local
   ```

Or symlink the skills directory into your project:

```bash
ln -s /path/to/agent-skills/skills .cursor/skills/agent-skills
```

Each skill must remain a folder containing `SKILL.md` (for example
`.cursor/skills/using-skills/SKILL.md`).

### From Cursor Settings (Remote Rule)

You can also import from GitHub in the UI:

1. Open **Cursor Settings → Rules, Commands**
2. Click **+ Add Rule** next to **Project Rules**
3. Select **Remote Rule (Github)**
4. Enter `https://github.com/pidashin/agent-skills`

This imports rule files from the repository. For the full skill pack, prefer
`gh skill install` so every lifecycle skill lands in `.cursor/skills/`.

---

## Project Rules and AGENTS.md

Skills hold workflows. Rules hold always-on project context. Keep rules short;
let skills carry the heavy lifecycle work.

### AGENTS.md (simplest)

Add or update `AGENTS.md` at your project root. Cursor reads it automatically.
Point the agent at the meta-skill:

```markdown
Read and follow the agent-skills lifecycle for all coding tasks.
Start with `using-skills` to determine which skill applies to the current step.
See AGENTS.md in the agent-skills repository for the five non-negotiables.
```

When skills are installed via `gh skill`, the agent discovers them by name and
description — you do not need to copy skill bodies into `AGENTS.md`.

### `.cursor/rules/` (optional, project-specific)

For conventions that should shape every session (stack, commands, boundaries),
add a focused rule in `.cursor/rules/`:

```bash
.cursor/rules/
  agent-workflow.mdc
```

Example `agent-workflow.mdc`:

```markdown
---
description: Core agent workflow and non-negotiables for this project
alwaysApply: true
---

- Load `using-skills` at session start to route to the correct lifecycle skill.
- Follow the five non-negotiables in AGENTS.md without exception.
- Do not preload every skill — load one skill at a time for the current step.
```

Project rules must use the `.mdc` extension with YAML frontmatter. Plain `.md`
files in `.cursor/rules/` are not loaded as rules. For simple instructions
without frontmatter, use `AGENTS.md` instead.

---

## How It Works in Cursor

Cursor uses **progressive disclosure** for skills — the same pattern this
repository is built around.

### 1. Onboarding (first adoption in a project)

Before using the lifecycle on a new repo, run onboarding. In Agent chat:

```
/init
```

Or type `/init` and select the skill. This runs the `init` skill, which:

- Creates or updates level 1 rules files (`AGENTS.md`, `.cursor/rules/`, etc.)
- Establishes the domain knowledge store location
- Checks GitHub CLI readiness when the project uses GitHub

After the project grows or agent output drifts from conventions, run `/context`
(the `context-engineering` skill) to audit rules, refresh the project map, and
pack selective context.

### 2. Session start

At the start of every Agent session:

1. Cursor applies `AGENTS.md` and any always-on project rules.
2. Skill **names and descriptions** are available to the agent (lightweight metadata).
3. The agent loads **one** full skill when the current step needs it — not the
   entire `skills/` tree.

Run the **Session Start Checklist** from `using-skills` before writing code:
confirm scope, check for conflicts, pick the single skill for the first step,
and define how you will verify that step is done.

### 3. Progressive skill activation

Instead of loading all skills into context, the agent activates skills by phase:

```
spec → plan → build* → review* → retro → ship (git)
```

`*build` = load `source-grounded-development`, `incremental-implementation`,
and/or `test-driven-development` only when their triggers match the current
slice — not all three by default.

`*review` = load `code-review-and-quality` before merge; add
`code-simplification` only when review findings require clarity work.

| Phase | Skill | When to load |
|-------|-------|--------------|
| Meta | `using-skills` | Session start; unsure which skill applies |
| Onboard | `init` | First adoption of this skill pack in the project |
| Context | `context-engineering` | Rules or project map are stale |
| Spec | `spec-driven-development` | Requirements or acceptance criteria missing |
| Plan | `planning-and-task-breakdown` | Spec confirmed; work needs decomposition |
| Build | `source-grounded-development` | External APIs, libraries, or frameworks |
| Build | `incremental-implementation` | Multi-file or large implementation |
| Build | `test-driven-development` | Logic, behaviour, or bug-fix work |
| Review | `code-review-and-quality` | Before merge |
| Review | `code-simplification` | Review flagged readability or complexity issues |
| Retro | `retrospective-and-knowledge-capture` | Decisions or gotchas worth preserving |
| Ship | `git-and-commit-discipline` | Committing or opening a PR |

---

## Invoking Lifecycle Skills

Type `/` in Agent chat to search for a skill by name. Common entrypoints:

| Type in chat | Skill |
|--------------|-------|
| `/using-skills` | Route to the correct skill; enforce non-negotiables |
| `/init` | One-time project onboarding |
| `/context-engineering` | Audit rules and refresh project context |
| `/spec-driven-development` | Define acceptance criteria before code |
| `/planning-and-task-breakdown` | Break a spec into verifiable tasks |
| `/code-review-and-quality` | Multi-axis review before merge |
| `/code-simplification` | Simplify without changing behaviour |
| `/retrospective-and-knowledge-capture` | Capture decisions and gotchas |
| `/git-and-commit-discipline` | Atomic commits and reviewable PRs |

For build work, start with `using-skills` and load build skills only when their
triggers match — do not run all build skills by default.

### Optional: `.cursor/commands/` wrappers

If you prefer short slash-command names (for example `/context` instead of
`/context-engineering`), add thin command files under `.cursor/commands/`.
Commands are plain Markdown; Cursor can migrate them to skills with
`/migrate-to-skills` if you want everything under `.cursor/skills/`.

This repository ships Claude command entrypoints in [`.claude/commands/`](../.claude/commands/)
as a reference for the prompts each lifecycle step should run.

---

## User vs Project Scope

| Scope | Install path | Best for |
|-------|--------------|----------|
| **User** (`--scope user`) | `~/.cursor/skills/` | Your personal workflow across all repos |
| **Project** (`--scope project`) | `.cursor/skills/` in the repo | Team-shared skills checked into git |

A common setup:

- **User scope:** install the full `agent-skills` pack once for yourself.
- **Project scope:** add only `AGENTS.md` and project-specific rules; optionally
  add repo-specific skills alongside the shared pack.

---

## Verifying the Setup

1. Open **Cursor Settings → Rules, Commands** (Cmd+Shift+J on Mac).
2. Under **Agent Decides**, confirm skills such as `using-skills` and `init`
   appear with their descriptions.
3. Start a new Agent chat and type `/using-skills` — the agent should follow
   the meta-skill workflow.
4. On a new project, run `/init` and confirm rules files are created or updated.

---

## Rules vs Skills in Cursor

| | Rules (`AGENTS.md`, `.cursor/rules/*.mdc`) | Skills (`.cursor/skills/*/SKILL.md`) |
| --- | --- | --- |
| When loaded | Every session (if `alwaysApply`) or by glob/description | On relevance, or when you type `/skill-name` |
| Purpose | Always-on conventions and boundaries | Lifecycle workflows with exit criteria |
| Context cost | Always uses context when applied | Metadata always light; full body on demand |
| Best for | Stack, commands, scope limits | Spec, plan, build, review, retro, ship |

Keep rules minimal. Put procedural lifecycle content in skills, not in rules.

---

## Further Reading

- [Getting Started](./getting-started.md) — skill architecture and adoption workflow
- [Cursor: Agent Skills](https://cursor.com/docs/context/skills) — official skill format and directories
- [Cursor: Rules](https://cursor.com/docs/context/rules) — project rules and `AGENTS.md`
- [GitHub CLI: gh skill install](https://cli.github.com/manual/gh_skill_install) — install flags and agent targets
