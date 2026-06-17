---
name: init
description: One-time setup: level 1 rules files, domain knowledge store, and GitHub CLI readiness
---

Invoke the agent-skills:init skill.

Inspect the current project for existing agent instruction files and knowledge
stores. If the project uses GitHub, check whether GitHub CLI is installed,
authenticated, and has `gh skill` available. Ask before installing `gh`, upgrading
it, or running `gh auth login`.

Create or merge level 1 rules files (AGENTS.md, CLAUDE.md, or tool-specific
equivalents) with tech stack, commands, conventions, boundaries, and one example
pattern from the codebase when available. Do not add configs for tools the project
does not use.

Ask the user whether domain knowledge should be stored locally, remotely, through
another custom workflow, or skipped for now. Do not choose the location silently.

After the user decides, create or update only the necessary files. Preserve
existing instructions. Report created or updated files, rules coverage, GitHub CLI
readiness, domain knowledge location, access status, and any follow-up needed.
Remind the user to use context-engineering for ongoing maintenance.
