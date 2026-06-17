---
description: Implement the current slice with only the build skills that apply
---

Use `using-skills` to identify which build skill(s) apply to this step, then
load and follow only those skills — one at a time:

- `source-grounded-development` — only if the slice uses external APIs,
  libraries, or framework features (verify in official docs first)
- `incremental-implementation` — only if the slice is multi-file or too large
  for one pass
- `test-driven-development` — only if the slice changes logic or behaviour

Do not load all three by default. Stay within the agreed scope.
