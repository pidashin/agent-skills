# Using agent-skills with Antigravity CLI

Installing this repository as a native plugin in the Antigravity CLI (`agy`)
gives the agent access to structured workflows, slash commands, and lifecycle
gates directly within its execution environment.

---

## Installation

### From GitHub with `gh skill` (Recommended)

Install all skills for Antigravity with the GitHub CLI:

```bash
gh skill install pidashin/agent-skills --all --agent antigravity
```

To inspect before installing:

```bash
gh skill preview pidashin/agent-skills
```

`gh skill` is a preview GitHub CLI feature. Confirm it exists in your local `gh`
version before depending on it.

### From the Remote Repository with `agy`

Register the plugin globally in your Antigravity environment:
```bash
agy plugin install https://github.com/pidashin/agent-skills.git
```

### From a Local Clone

If you are developing or customizing the skills locally:
1. Clone the repository:
   ```bash
   git clone https://github.com/pidashin/agent-skills.git
   ```
2. Install the plugin using `agy`:
   ```bash
   agy plugin install /path/to/agent-skills
   ```

Verify that the plugin is installed and active:
```bash
agy plugin list
```

---

## How It Works in Antigravity

Antigravity natively supports the `agent-skills` pattern through **progressive disclosure** and **session-start lifecycle hooks**:

### 1. Onboarding Phase (Initialization)
Before adopting the skills in a new repository, run the onboarding command:
```bash
/init
```
This command helps configure project-specific settings, including:
- Creating **level 1 rules files** with stack, commands, conventions, and boundaries.
- Establishing the **designated domain knowledge store** (e.g., `docs/knowledge/` or a remote pointer).
- Linking `AGENTS.md` to enforce the five non-negotiables.

After the project grows or agent output drifts, run `/context` to audit rules files,
update the project map, and pack selective context for the current task.

### 2. Startup Verification
At the start of every session:
1. Antigravity reads `AGENTS.md` at the root of the workspace.
2. It loads the meta-skill (`skills/using-skills/SKILL.md`) to establish the **five non-negotiables**.
3. The agent runs the **Session Start Checklist** to verify scope boundaries and confirm verification criteria before writing any code.

### 3. Progressive Skill Activation
Instead of loading all skills into the prompt context at once—which consumes excessive tokens—Antigravity dynamically loads the active skill based on the current phase:

```
spec → plan → build* → review* → retro → ship (git)
```

`*build` = load `source-grounded-development`, `incremental-implementation`, and/or `test-driven-development` only when their triggers match the current slice — not all three by default.

`*review` = load `code-review-and-quality` before merge; add `code-simplification` only when review findings require clarity work.

- When defining requirements, the agent loads `spec-driven-development`.
- When breaking down tasks, the agent loads `planning-and-task-breakdown`.
- When implementing code, the agent loads only the build skill(s) whose triggers match (external APIs → `source-grounded-development`; multi-file → `incremental-implementation`; logic/behaviour → `test-driven-development`).
- When implementation is done, the agent loads `code-review-and-quality` before merge; `code-simplification` only if review findings require it.
- When development and verification are done, the agent loads `retrospective-and-knowledge-capture` to record design decisions and trade-offs.
- When committing and preparing a PR, the agent loads `git-and-commit-discipline`.

---

## Slash Commands

The plugin registers the lifecycle slash commands directly in the Antigravity shell:

| Command | Lifecycle Phase | Activated Skill |
|---------|-----------------|-----------------|
| `/init` | Onboarding | `init` |
| `/context` | Context maintenance | `context-engineering` |
| `/spec` | Spec | `spec-driven-development` |
| `/planning` | Plan | `planning-and-task-breakdown` |
| `/build` | Build | Build skills as triggered (see `using-skills`) |
| `/review` | Review | Review skills as triggered (see `using-skills`) |
| `/retro` | Retro | `retrospective-and-knowledge-capture` |
| `/ship` | Ship | `git-and-commit-discipline` |
