---
name: pr-standards
description: Pull-request structure, description template, and review-readiness checklist.
estimated-tokens: 700
used-by: [engineer, coder, architect, reviewer]
---

# Pull Request Standards

## Title

- Conventional-Commits style: `<type>(<scope>): <subject>`
- ≤ 72 characters.
- Imperative mood, lowercase after the type.

## Description template

```markdown
## Summary
- [1–3 bullets — what changed and why]

## Why
[problem this solves; link issue if relevant]

## Approach
[notable decisions, trade-offs, alternatives rejected]

## Test plan
- [ ] [test step]
- [ ] [test step]

## Risk
[deployment risk, rollback plan, feature flag if any]

## Screenshots / output
[if UI or CLI change]
```

## Scope hygiene

- One PR, one logical change.
- Refactors live in separate PRs from feature work.
- Generated files (lockfiles excepted) and unrelated formatting do not belong in the diff.

## Pre-submit checklist

- [ ] Builds locally.
- [ ] Tests pass locally.
- [ ] No `console.*` / `print` / `debugger` artifacts in the diff.
- [ ] No commented-out code in the diff.
- [ ] No new top-level dependency without flagging it in the description.
- [ ] No secrets in the diff.
- [ ] Description explains *why*, not just *what*.
- [ ] Reviewer can reproduce the test plan.

## Review etiquette

- Respond to every review comment — apply, push back with reasoning, or mark as out-of-scope.
- "Resolve" only after the reviewer agrees, not unilaterally.
- New review rounds: small follow-up commits, not amend-and-force-push.
