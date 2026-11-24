---
title: "Process feedback"
date: 2026-11-22
lastmod: 2026-11-22
description: "AI-written: a skill that reviews Claude Code's saved memories and decides what to do with each one: keep it, move it to a rules file, agent, skill, or hook, merge it, or delete it."
ShowReadingTime: false
ShowShareButtons: false
disableShare: true
ShowPostNavLinks: false
ShowBreadCrumbs: true
ShowWordCount: false
hideMeta: true
madeBy: "setup-share"
---

> **Written by AI.** This page was generated from Orlando's own setup by an automated run, and checked for private details before it was published. <a href="/ai-setup/#how-this-section-is-made">How this section is made</a>. Last updated 2026-11-22.

It shows only the proposed changes, waits for approval, and then applies them so memory stays lean.

**Works with:** <a href="/ai-setup/skills/tune-setup/">Tune setup</a>.

## <code>SKILL&#46;md</code>

The instructions for the skill that reviews saved memories and promotes, merges, or deletes them so the memory index stays small. It is run by hand, usually as part of a regular setup review.

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere">---
name: process-feedback
description: Review memories, promote lasting lessons to the right place, and prune the rest so memory stays lean
disable-model-invocation: true
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, AskUserQuestion
---

Claude Code&#x27;s auto memory captures corrections as `feedback` memories, and they take effect in the next session on their own. This review keeps them from piling up: every memory index line loads in every session. It runs as part of each `/tune-setup`, or when Orlando asks.

Read every memory file, of every type (`user`, `feedback`, `project`, `reference`), and its index entry in:

- `~/.claude/projects/&lt;workspace-memory-folder&gt;/memory/` (workspace memory, loads in every `~/ai` session)
- `~/.claude/agent-memory/&lt;name&gt;/` for each agent (loads for that agent)

If there are none, say so and stop.

For each one, decide on one action:
- **Keep as memory** if it&#x27;s a preference or lesson that&#x27;s working where it is.
- **Promote to `~/.claude/CLAUDE.md`** if it applies to all work everywhere.
- **Promote to `~/ai/CLAUDE.md`** if it applies to every agent in the workspace.
- **Promote to an agent definition** (`~/.claude/agents/&lt;name&gt;.md`) if it&#x27;s a standing rule for one agent&#x27;s role.
- **Promote to a skill** if it belongs to a workflow that only runs when invoked.
- **Turn into a hook** if it must hold every time and can be checked mechanically.
- **Merge** with another memory that says the same thing.
- **Delete** if it was a one-off, is stale, or is already covered.

A memory that repeats a rule already in a CLAUDE.md or agent file means the prose rule isn&#x27;t working: say so and consider a hook or clearer wording instead of another copy.

Default to the narrowest place that fully covers the lesson. Every line in a CLAUDE.md or memory index costs context in every session, so lean toward merging and deleting.

Show only the memories you would change, grouped by action, one line each with its location and the reason. Then one line for how many stay as they are. Wait for my input before changing anything. After we agree, apply the changes. When a lesson is promoted, delete the memory and its index line so it isn&#x27;t loaded twice. Git history in `~/.claude` keeps a record of everything removed.
</code></pre>

Taken out: 1 path.
