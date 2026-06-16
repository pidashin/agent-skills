---
name: init
description: >
  Initializes a project for agent-skills adoption. Use when the user invokes
  /init or asks to set up project conventions, agent instruction files, and a
  designated domain knowledge store.
---

# Init

## Overview

Initialize the current project for agent-skills usage. Do not assume the target
project is empty. Inspect first, preserve existing instructions, and ask before
choosing where domain knowledge should live.

Set up the minimum project conventions needed for future agent sessions:

- Agent entrypoint instructions (`AGENTS.md`, `CLAUDE.md`, or the tool-specific
  equivalent already used by the project).
- A designated domain knowledge store for future retrospectives.
- Clear instructions telling agents where to read and write domain knowledge.

---

## When to Use

Use this skill when:

- The user invokes `/init`.
- A project is adopting this skill pack for the first time.
- The project needs a decision about where domain knowledge is stored.
- Agent entrypoint instructions need to name the active skills and knowledge
  store.

Do not use this skill for normal feature work after project conventions already
exist. Use `spec-driven-development`, `planning-and-task-breakdown`, or the
appropriate lifecycle skill instead.

---

## Workflow

### Step 1: Inspect the project

Before writing files, check for existing project instructions and knowledge
stores:

- `AGENTS.md`
- `CLAUDE.md`
- `.cursor/rules/`
- `.cursorrules`
- `.windsurfrules`
- `docs/`
- `knowledge/`
- `wiki/`
- README files that mention agent workflows or project knowledge

State what exists and what you are assuming before making changes.

### Step 2: Ask for the domain knowledge decision

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

### Step 3: Create or update only necessary files

Use the boring, smallest change that satisfies the user's decision.

For a local knowledge store:

- Create the chosen folder if it does not exist.
- Add a concise `README.md` that states what belongs there.
- Add an initial `INDEX.md` only if the folder needs an obvious landing page.
- Update the agent entrypoint instructions to name this folder as the designated
  domain knowledge store.

For a remote knowledge store:

- Update the agent entrypoint instructions with the remote location or pointer.
- Add a note that read/write access depends on the configured MCP or connector.
- If no connector is configured, list that as a follow-up instead of inventing
  configuration.

For another custom workflow:

- Record exactly what the user specified in the agent entrypoint instructions.
- Avoid adding extra structure unless the user asked for it.

For skip:

- Add a short note that no domain knowledge store is configured.
- Instruct future agents to ask before writing retrospective knowledge.

### Step 4: Preserve existing instructions

When updating `AGENTS.md`, `CLAUDE.md`, or tool-specific rule files:

- Append or merge a short section instead of replacing the file.
- Keep existing project-specific guidance intact.
- Do not rewrite unrelated rules.
- Do not add lifecycle skills or command references unless they are relevant to
  initialization.

### Step 5: Verify

Before finishing, report:

- Which files were created or updated.
- The selected domain knowledge location.
- Whether agent read/write access is local, remote verified, remote unverified,
  custom, or skipped.
- Any follow-up needed, especially MCP or connector setup for remote stores.

---

## Common Rationalizations

| Rationalization | Reality |
|-----------------|---------|
| "I'll just create `docs/knowledge/`; that's probably fine." | The storage location is a project convention. Ask before choosing. |
| "The remote doc exists, so agents can use it." | Existence is not access. Remote read/write access must be verified through MCP, connector, or tool configuration. |
| "I'll replace the old `AGENTS.md` with the standard one." | Preserve project-specific instructions. Merge only the necessary initialization guidance. |
| "I'll add all possible tool configs while I'm here." | Initialization should create only what the user selected and what the project needs now. |

---

## Red Flags

- You are about to choose a domain knowledge store without user confirmation.
- You are rewriting existing project instructions instead of appending or merging.
- You are claiming remote access works without verifying the configured tool.
- You are adding agent-tool files for tools the project does not use.
- You are changing unrelated docs, source files, or build configuration.

---

## Output Template

```markdown
## Init Summary

Created or updated:
- [file path]

Domain knowledge store:
- Location: [local path / remote URL or name / custom workflow / skipped]
- Access: [local / remote verified / remote unverified / custom / skipped]

Follow-up:
- [only if needed]
```

---

## Verification Checklist

```
□ Existing project instruction files were inspected before edits
□ Assumptions were stated before writing files
□ User chose local, remote, other, or skip for domain knowledge
□ Only necessary initialization files were created or updated
□ Existing instructions were preserved
□ Domain knowledge location and access status were reported
```
