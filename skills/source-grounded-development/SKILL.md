---
name: source-grounded-development
description: >
  Prevents hallucinated APIs, deprecated methods, and invented library features
  by requiring verification against official documentation before any external
  API, library, or framework feature is used in code. Load this skill whenever
  writing code that uses any dependency, external API, or framework — which is
  almost always. If you are about to import something, use this skill.
---

# Source-Grounded Development

AI agents are confidently wrong about APIs. They produce plausible-looking code that references methods that don't exist, parameters that were renamed, and patterns that were deprecated two major versions ago. This skill makes verification non-optional.

The rule is simple: **if you cannot point to where it lives in the official documentation, you do not use it.**

---

## When This Skill Activates

- You are about to `import` or `require` a library
- You are calling a method on an external dependency
- You are using a framework API (routing, state management, hooks, middleware, etc.)
- You are referencing configuration options for a tool (bundler, linter, test runner)
- You are writing SDK calls or HTTP integrations against a third-party service
- You are upgrading or changing the version of a dependency

---

## Process

### Step 1: Identify what needs verification

Before writing any code, list every external dependency, API, or framework feature the implementation will use. This includes:

- Package imports (`import X from 'some-package'`)
- Methods called on those packages (`X.doThing()`)
- Configuration keys and their expected values
- Environment variables or SDK initialisation patterns
- Any HTTP endpoint, response shape, or auth mechanism

If the list is long, prioritise the ones you are least certain about.

### Step 2: Verify each item against official sources

For each item on the list, find the source of truth. In order of preference:

1. **Official documentation site** — the package's own docs, not a tutorial or blog post
2. **Official GitHub repository** — the README, or the source code itself
3. **Package registry listing** — npm, PyPI, etc. for version-specific information

What counts as verified:
- The method/function name appears exactly as you intend to use it
- The parameters and their types match what you are passing
- The version of the documentation matches (or is compatible with) the version in use
- The feature is not marked as deprecated, experimental, or removed

What does **not** count as verified:
- You remember seeing it in a project before
- A Stack Overflow answer from an unspecified date
- A tutorial that doesn't cite a version
- Another AI agent told you it exists

### Step 3: State what you verified and what you didn't

Before writing the implementation, output a short verification summary:

```
Verified:
- router.get() — Express 4.x docs ✓
- res.json() — Express 4.x docs ✓
- req.params — Express 4.x docs ✓

Not verified / uncertain:
- express-validator's .body() chaining syntax — docs were ambiguous on v7 vs v6 behaviour
  → Will note in code and flag for review
```

Unverified items must be flagged in code with a comment:

```ts
// ⚠️ UNVERIFIED: confirm .body() chaining syntax against express-validator v7 docs
const { body } = require('express-validator');
```

### Step 4: Write the implementation

Write the code. Do not deviate from what was verified. If you encounter a gap mid-implementation that requires using something unverified, stop, flag it, and return to Step 2 before continuing.

### Step 5: Verify exit criteria

Before declaring the implementation done:

- [ ] Every import is verified against official docs
- [ ] Every method call uses verified parameter names and types
- [ ] All unverified items are flagged with `⚠️ UNVERIFIED` comments
- [ ] No item on the verification list was skipped

---

## Anti-Rationalization Table

| Excuse | Rebuttal |
|--------|----------|
| *"I'm sure this method exists — I've used it before."* | Memory is not documentation. APIs change. Check the version in this project against the current docs. |
| *"It's a well-known library, of course the API is stable."* | Stability is not universality. Even stable libraries have breaking changes between major versions. |
| *"I'll verify after I write it — it'll be faster."* | Writing unverified code first creates a debugging session where you don't know if the bug is in your logic or in the API call. Verify first. |
| *"The type definitions are good enough."* | Types lag behind docs. Deprecated methods keep their types. Generated types are wrong. The docs are the source of truth. |
| *"It's just a config option, it doesn't need to be verified."* | Configuration errors are some of the hardest to debug because they fail silently or with misleading errors. Verify config keys too. |
| *"The user said to just get it working quickly."* | Speed is not an excuse to hallucinate APIs. An hour of debugging an invented method is not fast. Five minutes of verification is. |

---

## Red Flags

- You are writing a method call that you have not seen in the official docs in this session
- You cannot remember which version of a package the project uses
- The library's docs have changed significantly between the version you recall and the version in `package.json`
- You are copying a pattern from somewhere other than the official docs
- The implementation "looks right" but you haven't actually checked

---

## Verification Checklist

```
□ Listed all external dependencies the implementation uses
□ Found official documentation for each
□ Confirmed method/function names match exactly
□ Confirmed parameter names and types match
□ Confirmed the documented version matches the project's version
□ Flagged any unverified items with ⚠️ UNVERIFIED comments
□ Did not use any item that could not be verified
```
