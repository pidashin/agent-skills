# Using agent-skills with Antigravity CLI

Installing this repository as a native plugin in the Antigravity CLI (`agy`)
gives the agent access to structured workflows, slash commands, and lifecycle
gates directly within its execution environment.

---

## Installation

### From the Remote Repository (Recommended)

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
- Establishing the **designated domain knowledge store** (e.g., creating a `KNOWLEDGE.md` at the project root or designating a folder).
- Linking the `AGENTS.md` file to enforce the five non-negotiables.

### 2. Startup Verification
At the start of every session:
1. Antigravity reads `AGENTS.md` at the root of the workspace.
2. It loads the meta-skill (`skills/using-skills/SKILL.md`) to establish the **five non-negotiables**.
3. The agent runs the **Session Start Checklist** to verify scope boundaries and confirm verification criteria before writing any code.

### 3. Progressive Skill Activation
Instead of loading all skills into the prompt context at once—which consumes excessive tokens—Antigravity dynamically loads the active skill based on the current phase:

```
spec → plan → build (source-grounded) → retro → ship (git)
```

- When defining requirements, the agent loads `spec-driven-development`.
- When breaking down tasks, the agent loads `planning-and-task-breakdown`.
- When implementing code with dependencies, the agent loads `source-grounded-development`.
- When development and verification are done, the agent loads `retrospective-and-knowledge-capture` to record design decisions and trade-offs.
- When committing and preparing a PR, the agent loads `git-and-commit-discipline`.

---

## Slash Commands

The plugin registers the lifecycle slash commands directly in the Antigravity shell:

| Command | Lifecycle Phase | Activated Skill |
|---------|-----------------|-----------------|
| `/init` | Onboarding | `init` |
| `/spec` | Spec | `spec-driven-development` |
| `/planning` | Plan | `planning-and-task-breakdown` |
| `/build` | Build | `source-grounded-development` |
| `/retro` | Retro | `retrospective-and-knowledge-capture` |
| `/ship` | Ship | `git-and-commit-discipline` |
