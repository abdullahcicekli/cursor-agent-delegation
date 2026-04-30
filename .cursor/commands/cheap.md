Token-saving mode for this task. Override default triage and route to the cheapest subagent that can plausibly handle the work:

1. Try `analyst` first if the task is investigation-shaped.
2. Try `coder` (Haiku) for any coding work, even if it looks borderline non-trivial. Hand back if the coder reports the task exceeds its scope.
3. Only escalate to `engineer` if `coder` explicitly cannot finish.
4. Do NOT invoke `architect` under any circumstance for this task.
5. Do NOT invoke `reviewer` automatically — only if I ask.

Use this for tasks where I'd rather get a "good enough" answer cheaply than a perfect answer expensively.

Task:
$ARGUMENTS
