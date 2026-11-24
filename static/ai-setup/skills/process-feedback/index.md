# Process feedback

- Kind: Claude Code skill
- Address: https://oneillo.com/ai-setup/skills/process-feedback/
- Last updated: 2026-11-22

> AI-written and cleaned from one person's working setup. Placeholders in angle brackets are not real values: ask your user for theirs. The newest version is at https://oneillo.com/ai-setup/skills/process-feedback/.

Reviews saved memories, promotes lasting lessons to the right place, and prunes the rest so memory stays lean.

- **What it is:** Process feedback is a Claude Code skill that reviews saved memories, moves lasting lessons to the right rules file, agent, skill, or hook, and deletes the rest.
- **How it starts:** You type `/process-feedback`. Claude doesn't start it by itself.
- **Where the file goes:** `~/.claude/skills/process-feedback/SKILL.md`
- **Needs:** Nothing extra
- **To make it yours:** Put your own values where you see `<workspace-project>`.
  Taken out for privacy: 1 path.

**Works with:**
- **Calls:** [Tune setup](https://oneillo.com/ai-setup/skills/tune-setup/).
- **Called by:** [Close shop](https://oneillo.com/ai-setup/skills/close-shop/), [Tune setup](https://oneillo.com/ai-setup/skills/tune-setup/), and [Workspace rules](https://oneillo.com/ai-setup/rules/workspace/).

## The skill file (`SKILL.md`)

The instructions for reviewing every saved memory and deciding whether to keep, promote, merge, or delete each one, run as part of a regular setup tune-up or on request.

```
---
name: process-feedback
description: Review memories, promote lasting lessons to the right place, and prune the rest so memory stays lean
disable-model-invocation: true
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, AskUserQuestion
---

Claude Code's auto memory captures corrections as `feedback` memories, and they take effect in the next session on their own. This review keeps them from piling up: every memory index line loads in every session. It runs as part of each `/tune-setup`, or when Orlando asks.

Read every memory file, of every type (`user`, `feedback`, `project`, `reference`), and its index entry in:

- `~/.claude/projects/<workspace-project>/memory/` (workspace memory, loads in every `~/ai` session)
- `~/.claude/agent-memory/<name>/` for each agent (loads for that agent)

If there are none, say so and stop.

For each one, decide on one action:
- **Keep as memory** if it's a preference or lesson that's working where it is.
- **Promote to `~/.claude/CLAUDE.md`** if it applies to all work everywhere.
- **Promote to `~/ai/CLAUDE.md`** if it applies to every agent in the workspace.
- **Promote to an agent definition** (`~/.claude/agents/<name>.md`) if it's a standing rule for one agent's role.
- **Promote to a skill** if it belongs to a workflow that only runs when invoked.
- **Turn into a hook** if it must hold every time and can be checked mechanically.
- **Merge** with another memory that says the same thing.
- **Delete** if it was a one-off, is stale, or is already covered.

A memory that repeats a rule already in a CLAUDE.md or agent file means the prose rule isn't working: say so and consider a hook or clearer wording instead of another copy.

Default to the narrowest place that fully covers the lesson. Every line in a CLAUDE.md or memory index costs context in every session, so lean toward merging and deleting.

Show only the memories you would change, grouped by action, one line each with its location and the reason. Then one line for how many stay as they are. Wait for my input before changing anything. After we agree, apply the changes. When a lesson is promoted, delete the memory and its index line so it isn't loaded twice. Git history in `~/.claude` keeps a record of everything removed.
```

Previous: [Close shop](https://oneillo.com/ai-setup/skills/close-shop/).
Next: [Save session](https://oneillo.com/ai-setup/skills/save-session/).

[Back to Skills](https://oneillo.com/ai-setup/skills/)

[Borrowed something? Tell me how it went](https://www.linkedin.com/in/oneillo/)

Text: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Scripts: [MIT](https://opensource.org/license/mit).
