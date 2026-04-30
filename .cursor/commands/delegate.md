Delegate the following task to the multi-agent team. Apply the triage rules from `.cursor/rules/00-delegation.mdc`:

1. Read the prompt below.
2. Detect any `xN` parameter at the end (e.g. `x3`, `x5`, `x7`, `x10`). If present, dispatch N agents in parallel using the distribution table.
3. If no `xN`, run normal triage: pick the cheapest single subagent that fits.
4. For non-trivial tasks (multi-file change, new feature, ambiguous scope), apply the Prompt Enrichment Protocol from `.cursor/rules/10-prompt-enrichment.mdc` first — ask 3–7 clarifying questions, propose a plan, wait for approval before dispatching.
5. After coding agents complete, invoke `reviewer` automatically unless the diff is < 5 lines or the user said "skip review".
6. Present a final summary to the user showing which agent handled what.

User prompt:
$ARGUMENTS
