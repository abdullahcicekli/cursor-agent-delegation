Route this task to the `analyst` subagent (read-only, cheap). Use for investigation, codebase exploration, dependency / security audits, library comparisons, and "how does X work" questions.

The analyst returns structured findings with citations. It will NOT edit code. If the investigation reveals a fix is needed, return the finding to me and I'll route the fix to the appropriate coding agent.

Investigation:
$ARGUMENTS
