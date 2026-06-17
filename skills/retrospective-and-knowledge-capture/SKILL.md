---
name: retrospective-and-knowledge-capture
description: >
  Guides the collection of retrospectives and domain knowledge after development.
  Captures design patterns, architectural decisions, trade-offs, and codebase gotchas
  to optimize future development cycles. Load this skill after development and
  verification are complete, before committing or opening a PR.
---

# Retrospective and Knowledge Capture

Codebases evolve, but the context behind architectural decisions, design patterns, and debugging discoveries is often lost. Without documentation, subsequent agents (or human developers) must spend time re-learning the system constraints, risking regressions or wasting tokens.

This skill ensures that key decisions, patterns, trade-offs, and gotchas are captured as concise, token-efficient domain knowledge at the end of each task.

---

## When This Skill Activates

- Implementation is complete and verification checks have passed.
- Review is complete (or skipped for trivial changes).
- You are about to enter the ship phase (`git-and-commit-discipline`).
- Tricky bugs, non-obvious design choices, or new patterns were introduced or resolved.

---

## Process

### Step 1: Identify Knowledge Candidates
Review the completed implementation and identify what is worth capturing for future tasks. Focus on:
- **Design Patterns**: Any recurring structures or architectural patterns applied (e.g., Repository pattern, state machine, pub-sub).
- **Nuances & Gotchas**: Unexpected system behavior, library quirks, or complex logic that took time to debug.
- **Dependencies**: Rationale for adding a new package or using a specific version/API path.

### Step 2: Extract the Rationale
For every key decision, document the "why" rather than the "what" (since the code and diff show the "what").
- What alternative approaches were considered?
- Why was this approach chosen over the others?
- What are the trade-offs of the chosen solution?

### Step 3: Format for Longevity and Token Efficiency
Write the knowledge capture entry. Keep it concise, structured, and easy for future LLM contexts to parse:
- Use clear headers and bullet points.
- Avoid verbose prose; write for a technical reader (another agent or senior engineer).
- Avoid screenshots or large binary files unless absolutely necessary.

**Example Entry Format:**
```markdown
### Feature: Password Reset flow
- **Design Pattern**: Used a stateless, single-use token verification table rather than expanding the main `Session` model.
- **Rationale**: Keeps session tracking independent; token verification is decoupled and can be rate-limited separately.
- **Gotchas**: Express-validator v7 body checks require explicit UTC conversion on date fields, otherwise the DB comparison fails silently.
```

### Step 4: Save to Designated Knowledge Store
Locate the project's designated domain knowledge store (which is determined and configured during project onboarding/initialization, e.g., via the project's onboarding guidelines, `AGENTS.md`, or custom workspace configuration).
- Save or append the captured knowledge entry to that store.
- Do not create arbitrary files outside the designated store.

---

## Anti-Rationalization Table

| Excuse | Rebuttal |
|--------|----------|
| *"I don't have time to write a retro/doc."* | A 5-minute markdown update saves hours of re-learning, debugging, and token costs in the next development cycle. |
| *"The code is self-documenting; the pattern is obvious."* | Code shows *what* you built, not *why* you built it or what alternatives you rejected. Rationale is invisible in the code. |
| *"The project doesn't have a designated knowledge store."* | Refer to the project's onboarding guidelines or ask the user to clarify where domain knowledge is recorded. |
| *"It was a simple task with no new learnings."* | If so, confirm with a one-line comment in the PR/commit. But if you spent even 5 minutes debugging a library or logic quirk, that is a learning worth capturing. |

---

## Verification Checklist

```
□ Identified and documented key design patterns and architectural choices
□ Documented the rationale, alternatives considered, and trade-offs
□ Recorded any unexpected gotchas, library bugs, or debugging discoveries
□ Kept descriptions concise and token-efficient
□ Saved or updated the record in the project's designated knowledge store
```
