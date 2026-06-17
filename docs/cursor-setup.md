# Using agent-skills with Cursor

Cursor discovers skills automatically and loads them on demand. Installing
**agent-skills** gives the agent structured workflows, lifecycle entrypoints, and
verification gates without stuffing every skill into context at session start.

---

## Installation

### Add marketplace (recommended)

1. In Cursor: **Settings → Plugins → Add marketplace**.
2. Paste the repository URL:
   ```
   https://github.com/pidashin/agent-skills
   ```
3. Install **agent-skills** from the marketplace panel.
4. Restart Cursor if skills do not appear immediately.
5. On each project, run `/init` once to create `AGENTS.md` and project rules.

Confirm under **Cursor Settings → Rules, Commands → Agent Decides** that skills
such as `using-skills` and `init` appear.

This installs a **custom marketplace** from the GitHub repo — not the public Cursor
Marketplace. No separate signup or submission is required on your side.

See also: [Claude Setup](./claude-setup.md) and [Antigravity Setup](./antigravity-setup.md)
for install steps on other tools.

### From a local clone

If you have cloned the repository locally (for example to customize skills before
installing):

1. Clone the repository:
   ```bash
   git clone https://github.com/pidashin/agent-skills.git
   ```
2. In Cursor: **Settings → Plugins → Add marketplace** → paste the **local path**
   to the clone.

Or symlink individual skills into a project:

```bash
ln -s /path/to/agent-skills/skills/using-skills .cursor/skills/using-skills
```

Each skill must remain a folder containing `SKILL.md` (for example
`.cursor/skills/using-skills/SKILL.md`).

### From Cursor Settings (Remote Rule)

You can import rules from GitHub without installing the full plugin:

1. Open **Cursor Settings → Rules, Commands**
2. Click **+ Add Rule** next to **Project Rules**
3. Select **Remote Rule (Github)**
4. Enter `https://github.com/pidashin/agent-skills`

This imports rule files only. For the full skill pack and slash commands, use
**Add marketplace** above.

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

When the plugin is installed, the agent discovers skills by name and
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

## User vs project scope

When installing from the marketplace panel, choose **user** scope to use
**agent-skills** across all projects on your machine, or **project** scope to
commit the plugin with a specific repository for your team.

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
- [Claude Setup](./claude-setup.md) — install via `/plugin marketplace add`
- [Antigravity Setup](./antigravity-setup.md) — install via `agy plugin install`
- [Cursor: Agent Skills](https://cursor.com/docs/context/skills) — official skill format and directories
- [Cursor: Rules](https://cursor.com/docs/context/rules) — project rules and `AGENTS.md`
