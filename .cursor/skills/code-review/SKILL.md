---
name: code-review
description: Review checklist with severity classification, plus the limit on revision rounds. Used by reviewer.
estimated-tokens: 1500
used-by: [reviewer, architect, engineer]
---

# Code Review

## Severity scale

| Tier | Meaning | Action |
|---|---|---|
| 🔴 Critical | Bug, security flaw, data loss, broken build/test | Reviewer applies the fix |
| 🟠 Major | Wrong abstraction, missing test for new behavior, bad error handling | Original agent fixes |
| 🟡 Minor | Style nit, naming improvement, small clarity win | Note only |
| 🔵 Suggestion | Optional improvement, future refactor candidate | Note only |

Reviewer fixes Critical immediately. Major findings are returned to the original agent for revision. Maximum **2** revision rounds before escalation to `architect`.

## Checklist

### Correctness

- [ ] Does it do what the task asked?
- [ ] Are edge cases handled (empty inputs, nulls, boundary values, error paths)?
- [ ] Are concurrent / async paths safe (no race, no leaked promises)?
- [ ] Are integer / overflow / off-by-one risks handled?

### Tests

- [ ] New behavior has tests.
- [ ] Tests cover both happy path and at least one failure mode.
- [ ] Tests do not rely on implementation details that will fragment on refactor.

### Code hygiene

- [ ] No `console.*` / `print` / `debugger` artifacts.
- [ ] No commented-out code.
- [ ] No new `any` / untyped escape hatches.
- [ ] No magic numbers.
- [ ] Naming is intention-revealing.

### Architecture

- [ ] Dependency direction is correct (no inward → outward leaks).
- [ ] No new circular imports.
- [ ] Public surface didn't grow without reason.
- [ ] No new top-level dependency snuck in undeclared.

### Security

- [ ] No secrets, tokens, or credentials in source.
- [ ] User input validated before use in queries / shell / file paths / URLs.
- [ ] No `eval` / dynamic code execution from untrusted input.
- [ ] Authorization checks present where state is mutated.

### Performance

- [ ] No N+1 query patterns introduced.
- [ ] No unbounded loops over external input.
- [ ] No redundant work in hot paths (re-rendering, repeated I/O).

## Review output format

```markdown
## Reviewer — Review Report

**Scope**: [files reviewed]
**Verdict**: ✅ Approve | 🟠 Changes requested | 🔴 Block

### 🔴 Critical
- `path:line` — [issue] — Applied fix: [...]

### 🟠 Major
- `path:line` — [issue] — Suggested fix: [...]

### 🟡 Minor
- `path:line` — [issue]

### 🔵 Suggestions
- `path:line` — [...]

### Summary
[1–3 sentences]
```

## Reviewer judgment

- Don't invent issues. If there's nothing to flag, ✅ Approve with a one-line summary.
- Don't rewrite correct code to match personal preference.
- Cite `file:line` for every finding — vague review notes are useless.
- Bias toward "fix it yourself" for Critical, "describe and hand back" for Major.
