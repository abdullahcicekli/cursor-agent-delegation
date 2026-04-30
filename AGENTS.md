# Agents

This repository ships a small team of subagents tuned for **token efficiency**: route every task to the cheapest agent that can do the job. Cursor's main agent reads each subagent's `description` and delegates automatically.

## The team

| Agent | Model | Cost | Used for |
|---|---|---|---|
| `architect` | Opus 4.7 | $$$$$ | architectural decisions, ADRs, multi-module structure, technology selection |
| `engineer` | Sonnet 4.6 | $$$ | non-trivial implementation, complex bug fixes, in-module refactors |
| `reviewer` | Sonnet 4.6 | $$$ | post-implementation code review, severity-classified findings |
| `coder` | Haiku 4.5 | $ | mechanical work — renames, scaffolds, single-file edits, lint fixes |
| `analyst` | Haiku 4.5 (read-only) | $ | research, codebase exploration, dependency / security audits |

## Triage rules (main agent)

For every prompt, the main agent (whatever model the chat is using) asks in order:

1. **Read-only investigation?** → `analyst`. Stop.
2. **Mechanical / single-file / boilerplate?** → `coder`. Stop.
3. **Architectural decision?** → `architect`. Stop.
4. **Otherwise** → `engineer`.
5. **After any coding agent**, invoke `reviewer` unless the diff is < 5 lines or the user said "skip review".

Doing the work directly, without delegating, is the **exception**. Skip delegation only when the task is < 3 trivial lines, no file mutation, and zero clarification needed.

## Parallel delegation (`xN`)

Append `xN` to a prompt to dispatch N agents in parallel:

| `xN` | architect | engineer | coder | analyst |
|---|---|---|---|---|
| `x2` | — | 1 | — | 1 |
| `x3` | 1 | 1 | — | 1 |
| `x5` | 1 | 1 | 1 | 2 |
| `x7` | 1 | 2 | 2 | 2 |
| `x10` | 2 | 3 | 2 | 3 |

Use the `/delegate` slash command for explicit invocation, or just append `xN` to any natural-language prompt.

## Slash commands

| Command | Behavior |
|---|---|
| `/delegate <task> [xN]` | Run normal triage; if `xN`, parallel dispatch |
| `/architect <task>` | Force route to architect (skip triage) |
| `/analyze <question>` | Force route to analyst (read-only) |
| `/review [scope]` | Run reviewer on recent changes |
| `/cheap <task>` | Force route to cheapest agent (no architect, no auto-review) |
| `/plan <task>` | Force prompt-enrichment / planning before any work runs |

## Rules loaded by Cursor

`.cursor/rules/` defines six rules. Three are always loaded (delegation playbook, git safety, project-context discovery); three are description-triggered (prompt enrichment, clean code, review chain). See each `.mdc` file for the full text.

## Skills

`.cursor/skills/` ships nine framework-agnostic skills: `clean-code`, `code-architecture`, `code-review`, `testing-standards`, `commit-standards`, `pr-standards`, `analysis`, `security`, `api-design`. Subagents reference these on demand; they are not pre-loaded.

## Project Context Discovery (PCD)

Every task starts by reading the host project's `README.md`, root `*.md` files, and `docs/` folder. Project-specific conventions override boilerplate defaults. Boilerplate structural rules (delegation, git safety, file ownership) are never overridden. See `.cursor/rules/02-project-context.mdc`.

## Prompt Enrichment Protocol (PEP)

For non-trivial tasks (new feature, multi-file change, ambiguous scope) the main agent asks 3–7 clarifying questions and proposes a plan before dispatching subagents. Bypass with "skip questions" / "just do it" / `/cheap`. See `.cursor/rules/10-prompt-enrichment.mdc`.

## Customizing

- **Different models?** Edit the `model:` field in each `.cursor/agents/*.md`. Use `inherit` to match the parent chat's model, `fast` for whatever Cursor maps as the cheap default, or any specific model ID Cursor accepts.
- **Don't want a tier?** Delete its agent file. The main agent will route around it.
- **Add a tier?** Drop a new file in `.cursor/agents/` with `name`, `description` (use the words "use proactively" or "always use for" to encourage auto-delegation), `model`, and instructions.
