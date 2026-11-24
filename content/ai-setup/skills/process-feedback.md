---
title: "Process feedback: a Claude Code skill that prunes memory and promotes lasting lessons"
date: 2026-11-22
lastmod: 2026-11-22
summary: "AI-written: Reviews saved memories, promotes lasting lessons to the right place, and prunes the rest so memory stays lean."
heading: "Process feedback"
linkTitle: "Process feedback"
author: "Orlando O'Neill"
images: ["/ai-setup/cards/skills/process-feedback.png"]
ShowReadingTime: false
ShowShareButtons: false
disableShare: true
ShowPostNavLinks: false
ShowBreadCrumbs: true
ShowWordCount: false
hideMeta: true
madeBy: "setup-share"
---

<div class="post-description">Reviews saved memories, promotes lasting lessons to the right place, and prunes the rest so memory stays lean.</div>

> **Written by AI.** This page was generated from Orlando's own setup by an automated run, and checked for private details before it was published. <a href="/ai-setup/#how-this-section-is-made">How this section is made</a>. Last updated 2026-11-22.

- **What it is:** Process feedback is a Claude Code skill that reviews saved memories, moves lasting lessons to the right rules file, agent, skill, or hook, and deletes the rest.
- **How it starts:** You type <code>&#47;process&#45;feedback</code>. Claude doesn't start it by itself.
- **Where the file goes:** <code>&#126;&#47;&#46;claude&#47;skills&#47;process&#45;feedback&#47;SKILL&#46;md</code>
- **Needs:** Nothing extra
- **To make it yours:** Put your own values where you see <code>&#60;workspace&#45;project&#62;</code>.
  Taken out for privacy: 1 path.

**Works with:** <a href="/ai-setup/skills/tune-setup/">Tune setup</a>.

## The skill file (<code>SKILL&#46;md</code>) {#skill-md}

<a href="#skill-md">Link to this file</a>

The instructions for reviewing every saved memory and deciding whether to keep, promote, merge, or delete each one, run as part of a regular setup tune-up or on request.

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere">---
name: process-feedback
description: Review memories, promote lasting lessons to the right place, and prune the rest so memory stays lean
disable-model-invocation: true
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, AskUserQuestion
---

Claude Code&#x27;s auto memory captures corrections as `feedback` memories, and they take effect in the next session on their own. This review keeps them from piling up: every memory index line loads in every session. It runs as part of each `/tune-setup`, or when Orlando asks.

Read every memory file, of every type (`user`, `feedback`, `project`, `reference`), and its index entry in:

- `~/.claude/projects/&lt;workspace-project&gt;/memory/` (workspace memory, loads in every `~/ai` session)
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
