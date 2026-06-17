---
name: review
description: Review the change before merge with only the review skills that apply
---

Use `using-skills` to identify which review skill(s) apply to this step, then
load and follow only those skills — one at a time:

- `code-review-and-quality` — before merge; assess correctness, readability,
  architecture, security, and performance
- `code-simplification` — only when review findings call for clarity work
  without changing behaviour

Run `code-review-and-quality` first. Do not load both by default.
