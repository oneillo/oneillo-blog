# Save session

- Kind: Claude Code skill
- Address: https://oneillo.com/ai-setup/skills/save-session/
- Last updated: 2026-11-27
- Also changed: 2026-11-22

> AI-written and cleaned from one person's working setup. Placeholders in angle brackets are not real values: ask your user for theirs. The newest version is at https://oneillo.com/ai-setup/skills/save-session/.

Saves a project's goal, decisions, and next steps at the end of a session so the next session can pick up where this one left off.

- **What it is:** Save session is a Claude Code skill that saves a project's context file at the end of a working session so the next session can continue.
- **How it starts:** You type `/save-session`. Claude doesn't start it by itself.
- **Where the file goes:** `~/.claude/skills/save-session/SKILL.md`
- **Needs:** Nothing extra
- **To make it yours:** Put your own values where you see `<project-folder>`.
  Taken out for privacy: 1 private project folder name.

**Works with:**
- **Called by:** [Workspace rules](https://oneillo.com/ai-setup/rules/workspace/).
- **Also mentions:** [Close shop](https://oneillo.com/ai-setup/skills/close-shop/).

## The skill file (`SKILL.md`)

The instructions Claude follows to write or update a project's context file, with its structure and the rules for keeping it short and accurate.

````
---
name: save-session
description: Saves project context at the end of a session so the next one can continue. Use when Orlando types /save-session; close-shop also runs it.
disable-model-invocation: true
allowed-tools: Read, Write, Edit, Glob, Grep, Bash
---

# Save Session Context

Review the full conversation and save a context file so a future session can pick up where this one left off.

## Project folder

The root of the project worked on this session (e.g. `~/ai/projects/<area>/<project-folder>/`, `~/ai/tools/<tool>/`). Judge from files read, edited or created, the working directory and directory changes, and project names or folders the user mentioned. If unsure, ask before proceeding.

## Write context.md

Create or update `context.md` at the folder's root with this structure:

```markdown
---
name: [project name]
description: [one-line project description]
last-updated: [YYYY-MM-DD]
---

## Goal
[What Orlando wants from this project, in his words where you have them.]

## Constraints
[Rules he set for this project.]

## Current State
[What exists now. What was accomplished in the most recent session.]

## Key Decisions
[Important choices made and their reasoning. Accumulate these across sessions. Don't discard prior decisions unless they've been reversed.]

## Next Steps
[What to pick up next time, in priority order.]

## Open Questions
[Anything unresolved or needing input.]
```

## Rules

- Existing file: read it first and merge. Keep prior decisions and still-relevant context. Update `last-updated`.
- Current State describes the project now: rewrite it each time, don't stack dated updates.
- Before writing that something is or isn't done, check the files, not your memory of the conversation.
- Concise: an LLM reads this at session start; it is not a history. Only what helps a future session: no ephemeral details, nothing derivable from the code, git history or existing project files.
````

Previous: [Process feedback](https://oneillo.com/ai-setup/skills/process-feedback/).
Next: [Tune setup](https://oneillo.com/ai-setup/skills/tune-setup/).

[Back to Skills](https://oneillo.com/ai-setup/skills/)

[Borrowed something? Tell me how it went](https://www.linkedin.com/in/oneillo/)

Text: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Scripts: [MIT](https://opensource.org/license/mit).
