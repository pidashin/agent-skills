---
name: init
description: >
  Initializes a project for agent-skills adoption. Use when the user invokes
  /init or asks to set up must-have level 1 context: agent rules files, the
  designated domain knowledge store location, and GitHub CLI readiness. For
  ongoing context maintenance as the project grows, use context-engineering.
---

# Init

## Overview

Initialize the current project for agent-skills usage. Init creates the **must-have
baseline** — level 1 rules files and a designated domain knowledge store — so
future sessions start with persistent, project-wide context.

Do not assume the target project is empty. Inspect first, preserve existing
instructions, and ask before choosing where domain knowledge should live.

For keeping context accurate as features ship and the codebase grows, use
`context-engineering` after init — not another run of init.

Set up the minimum project conventions needed for future agent sessions:

- Agent entrypoint rules files (`AGENTS.md`, `CLAUDE.md`, or the tool-specific
  equivalent already used by the project).
- Tech stack, commands, conventions, boundaries, and one example pattern in those
  rules files.
- A designated domain knowledge store location for specs, architecture notes, and
  future retrospectives.
- Clear instructions telling agents where to read and write domain knowledge.
- GitHub CLI readiness for GitHub-hosted skill installation and updates.

---

## When to Use

Use this skill when:

- The user invokes `/init`.
- A project is adopting this skill pack for the first time.
- No rules file exists and the project needs a level 1 baseline.
- The project needs a decision about where domain knowledge is stored.
- Agent entrypoint instructions need to name the active skills and knowledge store.

Do not use this skill for:

- Ongoing context audits or session context packing → use `context-engineering`.
- Normal feature work → use `spec-driven-development`, `planning-and-task-breakdown`,
  or the appropriate lifecycle skill.

---

## What Init Sets Up vs What Comes Later

| Layer | Init (one-time) | Later (`context-engineering`, `retro`, lifecycle skills) |
|-------|-----------------|----------------------------------------------------------|
| Level 1 — Rules files | Create or merge stack, commands, conventions, boundaries, one pattern | Audit and patch when repo drifts |
| Level 2 — Knowledge store | Choose location; add README; optional empty INDEX scaffold | Extend project map; add retro entries |
| Levels 3–5 — Task context | Point rules at source and knowledge store | Pack per task; manage sessions |

See `context-engineering` for the full context hierarchy and ongoing maintenance
workflow.

---

## Workflow

### Step 1: Inspect the project

Before writing files, check for existing project instructions and knowledge
stores. Also identify whether the project has a GitHub remote.

**Rules and agent entrypoints:**

- `AGENTS.md`
- `CLAUDE.md`
- `.cursor/rules/`
- `.cursorrules`
- `.windsurfrules`
- `.github/copilot-instructions.md`

**Knowledge and architecture:**

- `docs/`
- `knowledge/`
- `wiki/`
- README files that mention agent workflows or project knowledge

**Project signals for rules file content:**

- `package.json`, `pyproject.toml`, `Cargo.toml`, or equivalent manifest
- Common scripts in README or Makefile
- Existing lint/format config
- One representative source file that shows the project's style

Run `git remote -v` if the project is a git repository.

State what exists, what is missing, and what you are assuming before making
changes.

### Step 2: Check GitHub CLI readiness

If the project uses GitHub, check whether GitHub CLI is installed, authenticated,
and capable of managing agent skills:

- `command -v gh`
- `gh --version`
- `gh auth status`
- `gh skill --help`

If `gh` is missing, ask the user before installing it. Use the official GitHub CLI
installation instructions for the user's operating system or package manager; do
not invent package commands. If the install command requires network access,
package-manager writes, or elevated permissions, ask before running it.

If `gh` is installed but authentication is missing or broken, guide setup with
`gh auth login`. Ask before starting login because it may open a browser, prompt
for credentials, or update git protocol settings.

If `gh` is authenticated but `gh skill` is unavailable, report that the installed
GitHub CLI does not support the preview `gh skill` command yet. Ask whether to
upgrade GitHub CLI or continue without GitHub skill automation.

Record the result as one of:

- `gh ready`: `gh` is installed, authenticated, and `gh skill` is available.
- `gh installed, auth needed`: `gh` exists but `gh auth status` is not healthy.
- `gh installed, skill unavailable`: `gh` exists but `gh skill` is unavailable.
- `gh missing`: `gh` is not installed.
- `not GitHub-hosted`: the project does not use a GitHub remote.

Do not block local initialization if the user chooses not to install, upgrade, or
authenticate `gh`. Record the follow-up instead.

### Step 3: Ask for the domain knowledge decision

Ask the user where domain knowledge should live. Offer these choices:

1. **Local, recommended:** create or use a local repo folder such as
   `docs/knowledge/`.
2. **Remote:** use an external store such as Google Drive, Notion, a wiki, or
   another document system. Ask which system, where it lives, and whether an MCP
   or other connector is already configured for agent read/write access.
3. **Other:** let the user describe a custom workflow.
4. **Skip for now:** record that no domain knowledge store is configured yet.

Do not choose silently. If the user selects a remote store, do not claim agents
can read or write it until the relevant MCP or connector has been configured and
verified in that project.

### Step 4: Create or update rules files (level 1)

Use the boring, smallest change that satisfies the user's decision. Rules files
are always loaded, project-wide. They should be concise and actionable, not a
dump of the entire codebase.

**Tool-specific entrypoints:**

| Tool | File |
|------|------|
| Claude Code | `CLAUDE.md` |
| Cursor | `.cursor/rules/*.md` or `.cursorrules` |
| Windsurf | `.windsurfrules` |
| GitHub Copilot | `.github/copilot-instructions.md` |
| OpenAI Codex / generic | `AGENTS.md` |

Create or update only the entrypoints the project actually uses. Do not add
configs for tools the project does not use.

**What to include in rules files:**

- Tech stack and major dependencies
- Common commands (build, test, lint, dev, type check)
- Code conventions the agent must follow
- Boundaries (secrets, schema changes, dependency additions, scope limits)
- One short example of a well-written pattern from this codebase, when available
- Pointer to the designated domain knowledge store from Step 3
- Pointer to `using-skills`, `context-engineering`, and lifecycle skills this
  project adopts

**Rules file template:**

```markdown
# Project: [Name]

## Tech Stack
- [Runtime, framework, language versions]
- [Database, ORM, key libraries]

## Commands
- Build: `[command]`
- Test: `[command]`
- Lint: `[command]`
- Dev: `[command]`
- Type check: `[command]`

## Code Conventions
- [Naming, export style, file layout]
- [Testing location and style]
- [Patterns specific to this repo]

## Boundaries
- Never commit secrets or `.env` files
- Ask before modifying [database schema / public API / etc.]
- Touch only what the task asks for
- Verify external APIs in official docs before use

## Domain Knowledge
- Store: [local path / remote pointer / skipped]
- Write retrospectives via `retrospective-and-knowledge-capture`
- Keep rules and project map current via `context-engineering`

## Patterns
[One short example of a well-written component or module in this project's style]
```

Inspect manifests, README, and one representative source file to fill in real
values. Do not invent stack details — state assumptions and ask when uncertain.

### Step 5: Set up the domain knowledge store

For a **local** knowledge store:

- Create the chosen folder if it does not exist.
- Add a concise `README.md` that states what belongs there (specs, architecture
  notes, retrospectives, project map).
- Add a minimal `INDEX.md` scaffold only when the codebase clearly has multiple
  areas — full map maintenance belongs in `context-engineering`.
- Update the rules file to name this folder as the designated domain knowledge
  store.

For a **remote** knowledge store:

- Update the rules file with the remote location or pointer.
- Add a note that read/write access depends on the configured MCP or connector.
- If no connector is configured, list that as a follow-up instead of inventing
  configuration.

For **another custom workflow**:

- Record exactly what the user specified in the rules file.
- Avoid adding extra structure unless the user asked for it.

For **skip**:

- Add a short note that no domain knowledge store is configured.
- Instruct future agents to ask before writing retrospective knowledge.

### Step 6: Note MCP and connector opportunities

For richer context beyond files in the repo, note MCP servers the project may
benefit from. Do not install or configure MCP servers without user approval.

| MCP / connector | What it provides |
|-----------------|------------------|
| Documentation (e.g. Context7) | Library docs fetched on demand |
| Browser / DevTools | Live DOM, console, network state |
| Database | Schema and query results |
| Filesystem / search | Project-wide file access |
| GitHub | Issues, PRs, repository context |
| Remote docs (Notion, Drive, etc.) | External knowledge store access |

If the user chose a remote knowledge store in Step 3, tie MCP setup to that
decision and record unverified access as follow-up.

### Step 7: Preserve existing instructions

When updating `AGENTS.md`, `CLAUDE.md`, or tool-specific rule files:

- Append or merge a short section instead of replacing the file.
- Keep existing project-specific guidance intact.
- Do not rewrite unrelated rules.
- Do not add lifecycle skills or command references unless they are relevant to
  initialization.

### Step 8: Verify

Before finishing, report:

- Which files were created or updated.
- Whether rules files cover stack, commands, conventions, and boundaries.
- GitHub CLI readiness status, if the project uses GitHub.
- The selected domain knowledge location.
- Whether agent read/write access is local, remote verified, remote unverified,
  custom, or skipped.
- Any follow-up needed, especially MCP or connector setup for remote stores.
- Remind the user to run `context-engineering` when the codebase grows or agent
  output starts drifting from conventions.

---

## Common Rationalizations

| Rationalization | Reality |
|-----------------|---------|
| "I'll just create `docs/knowledge/`; that's probably fine." | The storage location is a project convention. Ask before choosing. |
| "The remote doc exists, so agents can use it." | Existence is not access. Remote read/write access must be verified through MCP, connector, or tool configuration. |
| "I'll replace the old `AGENTS.md` with the standard one." | Preserve project-specific instructions. Merge only the necessary initialization guidance. |
| "I'll add all possible tool configs while I'm here." | Initialization should create only what the user selected and what the project needs now. |
| "GitHub CLI is probably installed." | Check `command -v gh`, `gh auth status`, and `gh skill --help`; do not assume. |
| "I'll install or log in to `gh` automatically." | Installing tools and authenticating accounts affects the user's machine and credentials. Ask first. |
| "I'll build the full project map now." | Init scaffolds the store; `context-engineering` maintains the map as features land. |
| "I'll run init again to refresh context." | Use `context-engineering` for audits and updates after the baseline exists. |

---

## Red Flags

- You are about to choose a domain knowledge store without user confirmation.
- You are rewriting existing project instructions instead of appending or merging.
- You are claiming remote access works without verifying the configured tool.
- You are adding agent-tool files for tools the project does not use.
- You are changing unrelated docs, source files, or build configuration.
- You are running package-manager install commands without user approval.
- You are starting `gh auth login` without user approval.
- No rules file exists after init completes.
- Rules file lists invented stack details you did not verify in the repo.

---

## Output Template

```markdown
## Init Summary

Created or updated:
- [file path]

Rules files:
- Entrypoints: [list]
- Covers stack, commands, conventions, boundaries: [yes / partial — what's missing]

GitHub CLI:
- Status: [gh ready / gh installed, auth needed / gh installed, skill unavailable / gh missing / not GitHub-hosted]
- Follow-up: [only if needed]

Domain knowledge store:
- Location: [local path / remote URL or name / custom workflow / skipped]
- Access: [local / remote verified / remote unverified / custom / skipped]

MCP / connectors:
- [configured / recommended follow-up / not applicable]

Next:
- Run `context-engineering` when conventions drift or after major features ship.

Follow-up:
- [only if needed]
```

---

## Verification Checklist

```
□ Existing project instruction files were inspected before edits
□ Assumptions were stated before writing files
□ GitHub remote usage was checked
□ GitHub CLI readiness was checked when the project uses GitHub
□ Missing `gh`, broken auth, or missing `gh skill` was handled with user approval or recorded as follow-up
□ User chose local, remote, other, or skip for domain knowledge
□ Rules files created or updated for tools the project actually uses
□ Rules files cover tech stack, commands, conventions, and boundaries (or gaps are reported)
□ Domain knowledge store created or documented with access status
□ Existing instructions were preserved
□ Domain knowledge location, rules coverage, and access status were reported
□ User reminded to use context-engineering for ongoing maintenance
```
