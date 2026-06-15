---
name: git-and-commit-discipline
description: >
  Enforces atomic commits, meaningful commit messages, and clean PR hygiene.
  Each commit should represent one logical change that can be understood,
  reviewed, and reverted independently. Load this skill before committing,
  writing commit messages, or opening pull requests.
---

# Git and Commit Discipline

A commit is a unit of meaning. It says: this is one logical change, here is what it does, here is why. A commit that contains three unrelated changes is not three things that happened to ship together — it is a merge of three stories that can no longer be told apart.

This skill keeps commits atomic, messages meaningful, and PRs reviewable.

---

## When This Skill Activates

- You are about to run `git commit`
- You are writing a commit message
- You are opening a pull request
- You have multiple unrelated changes staged together

---

## Atomic Commits

One commit = one logical change.

A logical change is a change that makes sense on its own and can be described in a single sentence without using "and" to mean "two different things."

**Atomic:**
- `feat: add POST /auth/reset-password endpoint`
- `fix: correct token expiry check to use UTC timestamps`
- `chore: add express-validator dependency`

**Not atomic:**
- `add reset password and fix some bugs and clean up the auth file`
- `WIP`
- `various fixes`

If you have staged changes that belong to more than one logical change, unstage and commit them separately.

---

## Commit Message Format

Use the conventional commits format:

```
<type>(<optional scope>): <short summary in present tense>

<optional body: why this change was made, not what — the diff shows the what>

<optional footer: breaking changes, issue references>
```

**Types:**
- `feat` — new behaviour or feature
- `fix` — bug fix
- `refactor` — code change with no behaviour change
- `chore` — tooling, dependencies, configuration
- `docs` — documentation only
- `test` — adding or fixing tests
- `perf` — performance improvement

**Rules for the summary line:**
- Present tense ("add" not "added", "fix" not "fixed")
- No period at the end
- Under 72 characters
- Describes what the commit does, not what you did

**Body (when needed):**
- Explain *why*, not *what* — the diff shows what
- Wrap at 72 characters

```
feat(auth): add password reset via email link

Implements the reset flow described in the spec. Token is stored in
reset_tokens table with a 1-hour expiry. Single-use enforcement is
handled at redemption time.

Closes #142
```

---

## PR Hygiene

- **One PR per task** — matches the task plan
- **Title = the main commit message** or a one-sentence summary of the task
- **Description includes:**
  - What this PR does (one paragraph)
  - How to verify it (test command or manual steps)
  - Any out-of-scope changes and why they were necessary
- **Size:** if the PR diff is too large to review in one sitting, split it

---

## Anti-Rationalization Table

| Excuse | Rebuttal |
|--------|----------|
| *"I'll clean up the commits before merging."* | The cleanup will not happen. Commit atomically now. It takes less time than squashing later and makes the history useful. |
| *"The message doesn't matter — the code is what counts."* | In three months, when something breaks, the commit message is the first place anyone looks. Write it for future-you. |
| *"It's all related — it goes in one commit."* | "Related" is not "atomic." If the commit message requires "and" to describe two different things, it's two commits. |
| *"The PR is large but it's one feature."* | A feature can be multiple PRs. Small PRs get reviewed; large PRs get rubber-stamped or blocked. |

---

## Commit Checklist

```
□ Only one logical change is staged
□ Commit type is correct (feat / fix / refactor / chore / docs / test / perf)
□ Summary line is under 72 characters, present tense, no period
□ Body explains why (if the why isn't obvious from the diff)
□ No unrelated changes are included
□ PR description includes what, how to verify, and any scope deviations
```
