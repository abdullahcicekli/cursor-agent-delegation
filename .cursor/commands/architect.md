Route this task directly to the `architect` subagent, bypassing triage. Use this command for explicit architecture work where you already know the cost is justified.

Apply Project Context Discovery first (`.cursor/rules/02-project-context.mdc`) — read the host project's README, root `*.md`, and `docs/` so the architect's recommendation lands in the project's actual conventions.

If the task is large enough to warrant a written ADR, ask the architect to write one to `docs/adr/NNNN-<slug>.md`.

Task:
$ARGUMENTS
