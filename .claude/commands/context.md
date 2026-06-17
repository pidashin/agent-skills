---
name: context
description: Audit and refresh rules files, project map, and session context
---

Invoke the agent-skills:context-engineering skill.

Locate existing rules files and the designated domain knowledge store. Audit level 1
rules against current repo reality — stack, commands, conventions, boundaries, and
example patterns. Patch only stale sections; preserve unrelated guidance.

Audit the project map or INDEX in the knowledge store. Add, update, or prune sections
when codebase areas changed. Do not bulk-load the entire knowledge store for one task.

Pack selective context for the current task: relevant source files, one pattern
example, and the applicable spec or map section. Surface confusion when spec, rules,
and code conflict instead of guessing.

Report what was audited, what was updated, and what context is loaded for the
current task. Recommend a fresh session if conversation context has drifted.
