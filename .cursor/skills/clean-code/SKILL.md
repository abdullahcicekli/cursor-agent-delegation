---
name: clean-code
description: SOLID, DRY, KISS, YAGNI, naming conventions, function and file size guidelines, error handling, and the hard list of code-hygiene prohibitions. Mandatory for every coding agent.
estimated-tokens: 2400
used-by: [architect, engineer, coder, reviewer]
---

# Clean Code

## Principles

| | Rule | Smell |
|---|---|---|
| **SRP** | One reason to change per module/function | Function does more than its name suggests |
| **OCP** | Extend without modifying | New feature = editing existing code |
| **LSP** | Subtypes substitute base | Override changes contract |
| **ISP** | No client depends on unused methods | Empty implementations forced |
| **DIP** | Depend on abstractions | Direct construction of dependencies |
| **DRY** | Extract on Rule of Three (3+ duplications) | Cargo-culted abstraction at first sight |
| **KISS** | Simplest correct solution wins | Clever > readable |
| **YAGNI** | No speculative code | "We might need this later" |

## Hard prohibitions

- **No `// what`-style comments.** Self-documenting names. Exception: a single line explaining a non-obvious *why* (workaround, invariant, hidden constraint). Doc comments on exported public APIs are fine.
- **No `console.*` / `print` debugging artifacts.** Use the project's logger.
- **No `debugger` statements.**
- **No commented-out code.** Use version control.
- **No `any` / `interface{}` / untyped escape hatches** without justification.
- **No magic numbers.** Named constants.
- **No `TODO` / `FIXME`** unless asked.

## Naming

| Element | Convention | Example |
|---|---|---|
| Files | `kebab-case` (or project default) | `user-service.ts` |
| Variables / functions | `camelCase` (or `snake_case` per language) | `getUserById` |
| Classes / types | `PascalCase` | `UserService` |
| Constants | `UPPER_SNAKE_CASE` | `MAX_RETRIES` |
| Booleans | `is*` / `has*` / `should*` | `isActive` |
| Event handlers | `handle*` | `handleSubmit` |

### Quality checks

- **Intention-revealing**: tells what, not how.
- **No abbreviations**: `button` not `btn`, `response` not `res`.
- **No generic names**: avoid `data`, `info`, `item`, `temp`, `val`.
- **One vocabulary per concept**: pick `fetch` or `get` or `retrieve` and stay consistent.

## Function rules

- **≤ 20 lines** (excluding type defs).
- **≤ 3 parameters** — use an options object beyond that.
- **≤ 2 levels of nesting** — early returns instead.
- **Cyclomatic complexity ≤ 8.**
- **One function, one job.** "and" in the name = split.

```ts
// good — early returns
function getDiscount(user: User): number {
  if (!user.isPremium) return 0;
  if (user.membershipYears < 1) return 5;
  if (user.membershipYears < 5) return 10;
  return 15;
}
```

## Error handling

- Every catch does something meaningful (log, transform, rethrow). Empty catch is forbidden.
- Domain-specific error types over generic `Error`.
- Separate user-facing from internal errors.
- Do not use exceptions for control flow.
- Result-type pattern is fine where the language supports it ergonomically.

## File structure

- **≤ 250 lines per file.** One concept per file.
- **Barrel exports** (`index.ts`) for module public APIs.
- **Co-locate**: tests next to source, types next to implementation.

## Imports

- No circular dependencies.
- No wildcard imports.
- Order: framework → third-party → internal → relative.
- Remove unused imports immediately.

## Pre-submit checklist

- [ ] No comments (other than one-line *why* notes or doc comments)
- [ ] No `console.*` / debug statements
- [ ] No `any` / untyped holes
- [ ] No magic numbers
- [ ] Functions ≤ 20 lines, files ≤ 250 lines
- [ ] All errors handled
- [ ] Naming intention-revealing
- [ ] No duplicated logic (rule of three)
- [ ] Single responsibility per file/function

> Thresholds are targets. Match project conventions where they differ. Hard prohibitions are non-negotiable.
