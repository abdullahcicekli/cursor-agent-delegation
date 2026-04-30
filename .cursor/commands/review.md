Invoke the `reviewer` subagent on recently changed files. Apply `.cursor/skills/code-review/SKILL.md` checklist with severity classification:

- 🔴 Critical → reviewer applies the fix
- 🟠 Major → reviewer hands back to original agent (max 2 rounds)
- 🟡 Minor → noted, not blocking
- 🔵 Suggestion → optional

If the user provided a scope (file path, directory, or "the diff"), restrict review to that scope. Otherwise review whatever was changed in this conversation.

Scope:
$ARGUMENTS
