# Process feedback

- Kind: Claude Code skill
- Address: https://oneillo.com/ai-setup/skills/process-feedback/
- Last updated: 2026-11-27
- Also changed: 2026-11-22

> AI-written and cleaned from one person's working setup. Placeholders in angle brackets are not real values: ask your user for theirs. The newest version is at https://oneillo.com/ai-setup/skills/process-feedback/.

Reviews saved memories, promotes lasting lessons to the right rules file, agent, skill, or hook, and prunes the rest.

- **What it is:** Process feedback is a Claude Code skill that reviews saved memories, moves lasting lessons to the right place, and deletes the rest.
- **How it starts:** You type `/process-feedback`. Claude doesn't start it by itself.
- **Where the file goes:** `~/.claude/skills/process-feedback/SKILL.md`
- **Needs:** Nothing extra
- **To make it yours:** Put your own values where you see `<project-memory-folder>`.
  Taken out for privacy: 1 path.

**Works with:**
- **Calls:** [Tune setup](https://oneillo.com/ai-setup/skills/tune-setup/).
- **Called by:** [Tune setup](https://oneillo.com/ai-setup/skills/tune-setup/) and [Workspace rules](https://oneillo.com/ai-setup/rules/workspace/).

## The skill file (`SKILL.md`)

The full instructions for the skill. It reads every saved memory, suggests one action for each, and applies the agreed changes.

```
---
name: process-feedback
description: Reviews memories, promotes lasting lessons to the right place, and prunes the rest. Use when Orlando types /process-feedback; tune-setup also runs it.
disable-model-invocation: true
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, AskUserQuestion
---

Auto memory captures corrections as `feedback` memories, effective next session on their own. This review keeps them from piling up: every memory index line loads in every session. Runs within each `/tune-setup`, or when Orlando asks.

Read every memory file of every type (`user`, `feedback`, `project`, `reference`) and its index entry in:

- `~/.claude/projects/<project-memory-folder>/memory/` (workspace memory, loads in every `~/ai` session)
- `~/.claude/agent-memory/<name>/` for each agent (loads for that agent)

None: say so and stop.

One action each:
- **Keep as memory:** a preference or lesson working where it is.
- **Promote to `~/.claude/CLAUDE.md`:** applies to all work everywhere.
- **Promote to `~/ai/CLAUDE.md`:** applies to every agent in the workspace.
- **Promote to an agent definition** (`~/.claude/agents/<name>.md`): a standing rule for one agent's role.
- **Promote to a skill:** belongs to a workflow that runs only when invoked.
- **Turn into a hook:** must hold every time and can be checked mechanically.
- **Merge** with a memory saying the same thing.
- **Delete:** one-off, stale, or already covered.

A memory repeating a rule already in a CLAUDE.md or agent file means the prose rule isn't working: say so and consider a hook or clearer wording, not another copy.

Default to the narrowest place that fully covers the lesson. Every CLAUDE.md or memory index line costs context every session, so lean toward merging and deleting.

Show only the memories you'd change, grouped by action, one line each with location and reason, then one line for how many stay. Wait for my input before changing anything; then apply what we agreed. A promoted lesson: delete the memory and its index line so it isn't loaded twice. Git history in `~/.claude` records everything removed.
```

Previous: [Close shop](https://oneillo.com/ai-setup/skills/close-shop/).
Next: [Save session](https://oneillo.com/ai-setup/skills/save-session/).

[Back to Skills](https://oneillo.com/ai-setup/skills/)

[Borrowed something? Tell me how it went](https://www.linkedin.com/in/oneillo/)

Text: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Scripts: [MIT](https://opensource.org/license/mit).
