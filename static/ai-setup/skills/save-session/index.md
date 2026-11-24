# Save session

- Kind: Claude Code skill
- Address: https://oneillo.com/ai-setup/skills/save-session/
- Last updated: 2026-11-22

> AI-written and cleaned from one person's working setup. Placeholders in angle brackets are not real values: ask your user for theirs. The newest version is at https://oneillo.com/ai-setup/skills/save-session/.

Saves where a project stands in a context file so the next session can pick up where this one left off.

- **What it is:** Save session is a Claude Code skill that writes a project's goal, decisions, and next steps into a context file at the end of a working session.
- **How it starts:** You type `/save-session`. Claude doesn't start it by itself.
- **Where the file goes:** `~/.claude/skills/save-session/SKILL.md`
- **Needs:** Nothing extra
- **To make it yours:** Put your own values where you see `<project-folder>`.
  Taken out for privacy: 1 private project folder pattern.

**Works with:**
- **Called by:** [Workspace rules](https://oneillo.com/ai-setup/rules/workspace/).

## The skill file (`SKILL.md`)

The instructions for saving a project's context at the end of a working session. It tells Claude how to find the project folder and write or update a context file there.

````
---
name: save-session
description: Save project context at end of a working session for continuity in future sessions
disable-model-invocation: true
allowed-tools: Read, Write, Edit, Glob, Grep, Bash
---

# Save Session Context

Review the full conversation and save a structured context file so a future session can pick up where this one left off.

## Determine the project folder

Identify the project the user has been working on this session. Look at:
- The files that were read, edited, or created during the conversation
- The working directory and any directory changes
- The user's explicit mentions of a project name or folder

The project folder is the root directory of whatever project the user was working on (e.g., `~/ai/projects/<area>/<project-folder>/`, `~/ai/tools/<tool>/`). If you cannot confidently determine the project folder, ask the user before proceeding.

## Write context.md

Create or update a `context.md` file in the root of the project folder.

Use this structure:

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

- If `context.md` already exists, read it first and merge. Don't overwrite prior decisions or context that's still relevant. Update the `last-updated` date.
- If this is the first time, create the file from scratch based on the conversation.
- Current State describes the project now. Rewrite it each time; don't stack dated updates.
- Before writing that something is done or not done, check the files. Don't rely on memory of the conversation.
- Be concise. This file is for an LLM to read at the start of a session, not a full history.
- Only include information that would actually help a future session. Skip ephemeral details.
- Do not include information that can be derived from the code, git history, or existing project files.
````

Previous: [Process feedback](https://oneillo.com/ai-setup/skills/process-feedback/).
Next: [Tune setup](https://oneillo.com/ai-setup/skills/tune-setup/).

[Back to Skills](https://oneillo.com/ai-setup/skills/)

[Borrowed something? Tell me how it went](https://www.linkedin.com/in/oneillo/)

Text: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Scripts: [MIT](https://opensource.org/license/mit).
