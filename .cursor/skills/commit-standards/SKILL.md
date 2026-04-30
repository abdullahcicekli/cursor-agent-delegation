---
name: commit-standards
description: Conventional Commits format and commit message guidance. Loaded during commit/PR phase.
estimated-tokens: 800
used-by: [engineer, coder, architect]
---

# Commit Standards

## Format (Conventional Commits)

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Types

| Type | Use for |
|---|---|
| `feat` | New feature |
| `fix` | Bug fix |
| `refactor` | Code change that neither fixes a bug nor adds a feature |
| `perf` | Performance improvement |
| `test` | Adding or fixing tests |
| `docs` | Documentation only |
| `build` | Build system / dependency changes |
| `ci` | CI configuration |
| `chore` | Routine maintenance, no production code change |
| `style` | Formatting, whitespace, missing semis — no logic change |

### Scope

Module, feature, or area name. Lowercase. Optional but encouraged.

### Subject line

- Imperative mood (`add`, not `added` / `adds`).
- Lowercase first word (after the type).
- No trailing period.
- ≤ 72 characters.

### Body

- Explain **why**, not what — the diff already shows what.
- Wrap at 72 columns.
- Separate from subject by a blank line.

### Footer

- Breaking changes: `BREAKING CHANGE: <description>`
- Issue refs: `Refs: #123` / `Closes: #456`
- Co-authors as needed.

## Examples

```
feat(auth): add refresh-token rotation

Tokens were single-use; mobile clients could not survive a network
blip without forcing a full re-login. Rotation extends the session
window without sacrificing the revocation properties of single-use.

Closes: #482
```

```
fix(billing): round subtotal before applying discount

Floating-point drift caused cents to disappear on multi-line orders.
Round to integer cents at line-item assembly; downstream tax and
discount math stays exact.
```

## Atomic commits

- One logical change per commit.
- Don't bundle a refactor with a feature; split.
- Tests for new behavior live in the same commit as the behavior, not a follow-up.

## Hard rules

- **Never commit secrets, credentials, or `.env` files.**
- **Never commit generated artifacts** (`dist/`, `build/`, `node_modules/`).
- **Never amend a published commit.**
- **Never bypass hooks** (`--no-verify`) without explicit user consent.
