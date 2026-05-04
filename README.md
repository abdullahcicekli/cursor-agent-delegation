# cursor-agent-delegation

> A drop-in multi-agent boilerplate for Cursor that automatically routes each prompt to the cheapest subagent that can do the job. Built around Cursor 2.4+ subagents and the `.cursor/rules/` system.

[![Cursor](https://img.shields.io/badge/Cursor-2.4%2B-blue)](https://cursor.com)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)

## What it does

You write one prompt. Cursor's main agent classifies it and delegates to the right subagent — Opus only when the task genuinely needs an architect, Haiku for boilerplate and research, Sonnet in between. Reviews are auto-invoked after coding work.

The point is **token efficiency**: stop spending Opus money on tasks Haiku could finish.

## What's in the box

```
.cursor/
├── agents/         5 subagents tuned for cost-tier routing
├── rules/          6 rule files (3 always-on, 3 triggered)
├── skills/         9 framework-agnostic skills
├── commands/       6 slash commands
└── mcp.json        empty MCP scaffold
AGENTS.md           team summary, also read by Cursor natively
```

## Quick start

### 1. Drop into your project

```bash
# fresh clone
git clone <this-repo> my-project
cd my-project

# or copy the boilerplate into an existing project
cp -r cursor-agent-delegation/.cursor your-project/
cp cursor-agent-delegation/AGENTS.md your-project/
```

### 2. Open in Cursor

Cursor 2.4 or newer. The agents, rules, skills, and commands are picked up automatically — no configuration UI needed.

### 3. Use it

```
# normal prompt — main agent auto-triages
fix the off-by-one in pagination

# force a tier
/architect design the event-sourcing layout for this module
/analyze how does session expiry work?
/cheap rename `getUser` to `fetchUser` across the codebase

# parallel multi-agent
implement the auth module x5
```

## How the routing works

Every prompt goes through this decision tree (encoded in `.cursor/rules/00-delegation.mdc`):

```
prompt
  ├─ read-only research?       → analyst   (Haiku, $)
  ├─ mechanical / boilerplate? → coder     (Haiku, $)
  ├─ architectural decision?   → architect (Opus, $$$$$)
  └─ otherwise                 → engineer  (Sonnet, $$$)
                                     ↓
                                 reviewer  (Sonnet, $$$, auto)
```

The main agent only does the work itself when the answer is < 3 trivial lines. Everything else delegates.

### Token-saving knobs

- **`/cheap <task>`** — forbids architect, prefers Haiku, skips auto-review.
- **Set the chat to a small model** — Cursor's main agent picks delegation, the chat model only runs the delegation logic itself.
- **`description: "skip review"`** in the prompt — bypasses reviewer.
- **Edit `model:` in `.cursor/agents/*.md`** — swap to `inherit` or any model you have access to.

## Rules — what's always loaded

| Rule | Always-on? | Purpose |
|---|---|---|
| `00-delegation.mdc` | yes | the triage playbook (~1.5K tokens) |
| `01-git-safety.mdc` | yes | no commits/pushes without consent |
| `02-project-context.mdc` | yes | read project README/docs at task start |
| `10-prompt-enrichment.mdc` | triggered | clarify before non-trivial work |
| `20-clean-code.mdc` | glob-triggered (source files) | code hygiene |
| `30-review-chain.mdc` | triggered | review chain protocol |

Always-on overhead is roughly 4–5K tokens. Description-triggered rules add only when relevant.

## Skills — framework-agnostic, on-demand

`clean-code`, `code-architecture`, `code-review`, `testing-standards`, `commit-standards`, `pr-standards`, `analysis`, `security`, `api-design`. Subagents reference these as they need them; the boilerplate is not Java-, Spring-, React-, or any-framework-specific.

If your project has its own conventions, drop them in `README.md`, `docs/`, or root `*.md` files — the **Project Context Discovery** rule reads those first and they override boilerplate defaults.

## Customizing

### Change which model each tier uses

Edit `.cursor/agents/<agent>.md`:

```markdown
---
name: engineer
description: ...
model: claude-sonnet-4-6   # ← swap to inherit / fast / any model ID Cursor accepts
readonly: false
is_background: false
---
```

### Drop a tier

Delete the agent file. The main agent routes around the missing tier.

### Add a tier

Drop a new file in `.cursor/agents/` with frontmatter and a clear description that includes the phrase "use proactively for …" or "always use for …" — Cursor uses the description to auto-delegate.

### Disable auto-delegation for one prompt

Tell the main agent: `do this yourself, no delegation` — the rule honors explicit override.

## Requirements

- Cursor IDE 2.4 or newer (subagents and skills support).
- Access to whichever models you reference in the agent files. Use `inherit` if you want everything to run on the chat's selected model.

## Design choices, briefly

- **No session memory**: Cursor handles conversation memory; storing artifacts duplicates state.
- **No display-name pool**: agents are functional roles, not characters.
- **No metrics framework**: token tracking belongs in Cursor's UI / billing, not in repo files.
- **No language-specific skills**: Java, Spring, React, etc. opinions live in the host project's docs, where they belong.
- **English only**: the boilerplate is generic; user-facing tone is whatever you set in your own project.

### Reference
- https://github.com/AFET-TEAM/copilot-agent-delegation

## License

MIT — see `LICENSE`.
