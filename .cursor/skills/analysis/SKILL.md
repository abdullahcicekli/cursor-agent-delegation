---
name: analysis
description: Report templates and citation discipline for read-only research tasks. Used by analyst.
estimated-tokens: 1000
used-by: [analyst]
---

# Analysis

## Output discipline

- **Cite every finding.** `path/to/file.ext:line` for code, full URL for external sources.
- **Confidence level**: High / Medium / Low — applied per finding, not per report.
- **Distinguish observation from inference.** "X imports Y" is observation; "therefore X depends on Y's behavior" is inference.
- **Mark assumptions explicitly.** If you guessed, say "assumption:" and mark it Low confidence.
- **Bullets over paragraphs.** Tight, scannable.

## Templates

### Codebase map

```markdown
## Codebase Map: [scope]

### Entry points
- `path/to/main.ts:1` — bootstraps the app
- `path/to/cli.ts:1` — CLI entry

### Modules
- `src/auth/` — authentication; depends on `src/users/`
- `src/users/` — user CRUD; depends on `src/db/`

### Hot spots (high-churn / high-coupling)
- `src/auth/session.ts` — imported by 14 files (High)

### Risks
- `src/legacy/` — undocumented, last touched 18 months ago (Medium)
```

### Dependency audit

```markdown
## Dependency Audit

| Package | Current | Latest | Risk | Notes |
|---|---|---|---|---|
| react | 18.2.0 | 19.0.0 | major bump, breaking | needs migration plan |
| lodash | 4.17.20 | 4.17.21 | safe patch | known prototype-pollution CVE in current |

### Recommendations
- Apply lodash patch immediately (CVE).
- React major bump → schedule with a feature freeze.
```

### Library / framework comparison

```markdown
## Comparison: [A] vs [B]

### Criteria
| | A | B |
|---|---|---|
| License | MIT | Apache 2.0 |
| Bundle size | 12kb | 38kb |
| Maintenance | active, monthly releases | active, quarterly |
| Ecosystem fit | matches our stack | requires adapter |

### Recommendation
[choice] because [primary reason]. [Secondary trade-off acknowledged].
```

### "How does X work" investigation

```markdown
## How [X] works

### Flow
1. Request hits `path/to/handler.ts:42` — auth middleware checks token
2. Handler delegates to `path/to/service.ts:18` — business logic
3. Service queries `path/to/repo.ts:7` — DB layer
4. Response formatted at `path/to/handler.ts:60`

### Key decisions
- Token format: JWT with 15-minute TTL (`path/to/auth.ts:10`)
- Refresh strategy: rotation on use (`path/to/auth.ts:55`)

### Gotchas
- Token blacklist is in-memory only — restarts wipe revocations (`path/to/auth.ts:80`)
```

## Constraints

- Read-only. Edits go to research output paths only (`.cursor/analysis/` if it exists, else inline in chat).
- Don't speculate without labeling. Don't fix issues you discover — describe them and hand back.
- Keep reports under ~600 words. If the topic is bigger, return an outline and ask which section to expand.
