---
name: planning-and-task-breakdown
description: >
  Decomposes a confirmed spec into small, ordered, independently verifiable
  tasks — each with its own scope boundary and acceptance criterion. Load this
  skill after spec-driven-development and before writing any code. Use when
  a feature involves more than one file, more than one system, or more than
  one step to verify.
---

# Planning and Task Breakdown

A plan is not a to-do list. A plan is a sequence of tasks, each small enough to verify individually, each with a defined scope boundary, each in the right order. The goal is to make every step reviewable and every failure localised.

---

## When This Skill Activates

- You have a confirmed spec and are ready to start implementation
- The task touches more than one file or system boundary
- The task will take more than one implementation step
- You are picking up a task mid-stream and need to understand what remains

---

## Process

### Step 1: Identify the slices

Break the spec into vertical slices — thin, end-to-end pieces of functionality that can each be built and verified independently. A slice is not a layer (don't slice by "frontend" then "backend"). A slice is a behaviour.

Ask: what is the smallest thing I can build and verify that moves the spec forward?

### Step 2: Write each task

Each task must have:

- **What**: one sentence describing what to implement
- **Scope**: what files or systems will be touched
- **Not touching**: what adjacent code will not be changed
- **Verify**: how to confirm this task is done (specific, testable)
- **Depends on**: which tasks must be done first (if any)

```markdown
### Task 1: Add the reset-password API endpoint

What: Create POST /auth/reset-password route that accepts email, generates a token, stores it, and sends the email.

Scope: src/routes/auth.ts, src/services/resetToken.ts (new file)

Not touching: src/routes/user.ts, src/models/user.ts, email template files

Verify:
- POST /auth/reset-password with a valid email returns 200
- POST /auth/reset-password with an unknown email returns 200 (no enumeration)
- A token record exists in the DB after the call
- An email is sent (mock in test, confirmed in integration)

Depends on: none
```

### Step 3: Order the tasks

List tasks in execution order. Each task should be independently mergeable. If two tasks must always be done together, they are one task.

### Step 4: Identify risks

For each task, note the highest-risk decision or dependency. These are the things most likely to cause a rework.

```
Risk: Token storage — need to confirm whether to use existing session store or a dedicated reset_tokens table. Decide before Task 1.
```

Risks are questions that need answers before the task begins, not after.

### Step 5: Confirm the plan

Present the task list with its risks. Ask: "Does this order make sense? Anything I'm missing or splitting wrong?"

Do not start coding until the plan is confirmed.

---

## Task Template

```markdown
### Task N: [short name]

**What:** [one sentence]

**Scope:** [files/modules that will be changed]

**Not touching:** [adjacent code that will not be changed]

**Verify:**
- [ ] [specific, testable check]
- [ ] [specific, testable check]

**Depends on:** [task numbers, or "none"]

**Risk:** [highest-risk decision or open question]
```

---

## Anti-Rationalization Table

| Excuse | Rebuttal |
|--------|----------|
| *"The plan is in my head, I'll just implement it."* | A plan in your head has no scope boundaries and no out-of-scope list. Write it down — it takes ten minutes and catches conflicts before they become bugs. |
| *"This is small enough to do in one shot."* | "One shot" is how you end up with a PR that changes twelve files and is impossible to review. Break it into steps even if the steps are small. |
| *"I'll figure out the order as I go."* | Figuring out the order mid-implementation is how you end up needing to undo work. Dependencies are cheaper to discover in planning than in code. |
| *"Plans change anyway."* | Plans change. That's fine — update the plan. An outdated plan is still better than no plan, because it makes the change visible. |

---

## Verification Checklist

```
□ Each task has a single, clear scope
□ Each task has an explicit "not touching" list
□ Each task has at least one specific, testable verify condition
□ Tasks are ordered so each one can be completed and verified independently
□ Risks and open questions are listed before starting
□ User has confirmed the plan
```
