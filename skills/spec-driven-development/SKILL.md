---
name: spec-driven-development
description: >
  Requires a written spec — objectives, acceptance criteria, scope boundaries,
  and out-of-scope list — before any code is written. Load this skill at the
  start of any new feature, significant change, or task where requirements
  are not yet written down. If you are about to write code without agreed
  acceptance criteria, use this skill first.
---

# Spec-Driven Development

Code written without a spec is a guess. A guess might be right. It is also the thing you rewrite when it turns out to be wrong. A spec takes five minutes. A rewrite takes hours.

This skill is not about writing long documents. It is about writing down what "done" means *before* starting, so that both you and the user agree on it.

---

## When This Skill Activates

- Starting a new feature or user-visible behaviour
- Picking up a task described in vague terms ("make it work better", "add X")
- Any change that touches more than one file or system boundary
- Before the `planning-and-task-breakdown` skill — spec comes first

---

## Process

### Step 1: Extract the goal

Ask (or infer from context): what problem does this solve for a user? Write one sentence.

```
Goal: Allow users to reset their password via email link.
```

If you cannot write this sentence without guessing, stop and ask. Do not proceed with a spec you invented.

### Step 2: Write acceptance criteria

List the specific, testable conditions that must be true for the task to be complete. Each criterion should be a statement you can verify as true or false.

```
Acceptance criteria:
- User can request a reset link using their registered email address
- Link expires after 1 hour
- Link is single-use — a second click shows an "already used" message
- Password must meet existing validation rules
- User receives a confirmation email after successful reset
```

Criteria that cannot be verified are not criteria — they are wishes. Rewrite them until they are testable.

### Step 3: Define scope boundaries

Write down explicitly what this task includes and what it does not include.

```
In scope:
- Reset flow for email/password accounts

Out of scope (will not be touched):
- OAuth/SSO accounts
- Admin password reset
- Email template styling
- Rate limiting (separate task)
```

The out-of-scope list is as important as the in-scope list. It is the thing that prevents scope creep mid-implementation.

### Step 4: Identify open questions

List anything that is currently unclear and needs a decision before or during implementation.

```
Open questions:
- Should we invalidate all existing sessions on password reset? (security policy)
- Where does the reset email come from — existing email service or new one?
```

Do not resolve open questions yourself. Bring them to the user.

### Step 5: Get confirmation

Present the spec. Ask: "Does this match what you want? Anything missing or wrong?"

Do not start coding until the spec is confirmed. A spec the user hasn't read is not a spec — it's a draft.

---

## Spec Template

```markdown
## Spec: [task name]

**Goal:** [one sentence — what problem does this solve?]

**Acceptance criteria:**
- [ ] [testable condition]
- [ ] [testable condition]

**In scope:**
- [what will be built/changed]

**Out of scope:**
- [what will not be touched]

**Open questions:**
- [anything that needs a decision]
```

---

## Anti-Rationalization Table

| Excuse | Rebuttal |
|--------|----------|
| *"The task is simple enough, I don't need a spec."* | Acceptance criteria still apply. Five lines is fine. Zero lines is not. "Simple" is how you end up rebuilding something because the user meant something different. |
| *"I already know what to build."* | Knowing what to build is not the same as the user agreeing on what to build. Write it down and confirm. |
| *"We can define done when we see the result."* | Defining done retrospectively is how you end up in a loop of "not quite right, try again." Agree on done before starting. |
| *"The ticket describes it well enough."* | Tickets describe intent, not acceptance criteria. Extract and confirm the criteria from the ticket before starting. |

---

## Verification Checklist

```
□ Goal is written in one sentence
□ Acceptance criteria are specific and testable (each one can be true or false)
□ In-scope list is explicit
□ Out-of-scope list exists and includes adjacent things that will NOT be touched
□ Open questions are listed (not resolved unilaterally)
□ User has confirmed the spec before coding began
```
