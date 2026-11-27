---
title: "Process feedback: a Claude Code skill that prunes memories and promotes lessons"
date: 2026-11-22
lastmod: 2026-11-27
summary: "AI-written: Reviews saved memories, promotes lasting lessons to the right rules file, agent, skill, or hook, and prunes the rest."
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

<div class="post-description">Reviews saved memories, promotes lasting lessons to the right rules file, agent, skill, or hook, and prunes the rest.</div>

<p class="ai-setup-label">Written by AI from Orlando's setup. Last updated <time datetime="2026-11-27">2026-11-27</time>. Also changed: <time datetime="2026-11-22">2026-11-22</time>. <a href="/ai-setup/#how-this-section-is-made">How this is made</a>.</p>

<section class="facts facts-skill">

<div class="facts-head"><span class="tag tag-skill">Skill</span><span class="facts-cmd"><code>/process-feedback</code></span></div>

- **What it is:** Process feedback is a Claude Code skill that reviews saved memories, moves lasting lessons to the right place, and deletes the rest.
- **How it starts:** You type <code>&#47;process&#45;feedback</code>. Claude doesn't start it by itself.
- **Where the file goes:** <code>&#126;&#47;&#46;claude&#47;skills&#47;process&#45;feedback&#47;SKILL&#46;md</code>
- **Needs:** Nothing extra
- **To make it yours:** Put your own values where you see <code>&#60;project&#45;memory&#45;folder&#62;</code>.
  Taken out for privacy: 1 path.

<div class="works">

**Works with:**
- **Calls:** <a href="/ai-setup/skills/tune-setup/">Tune setup</a>.
- **Called by:** <a href="/ai-setup/skills/tune-setup/">Tune setup</a> and <a href="/ai-setup/rules/workspace/">Workspace rules</a>.

</div>

</section>

<section class="file">

## The skill file (<code>SKILL&#46;md</code>) {#skill-md}

<a href="#skill-md">Link to this file</a>

The full instructions for the skill. It reads every saved memory, suggests one action for each, and applies the agreed changes.

<figure class="code">
<div class="code-bar"><span class="code-name">SKILL.md</span><span class="code-meta">Markdown · 31 lines</span><button type="button" class="ai-setup-copy copy" hidden>Copy</button></div>

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere">---
name: process-feedback
description: Reviews memories, promotes lasting lessons to the right place, and prunes the rest. Use when Orlando types /process-feedback; tune-setup also runs it.
disable-model-invocation: true
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, AskUserQuestion
---

Auto memory captures corrections as `feedback` memories, effective next session on their own. This review keeps them from piling up: every memory index line loads in every session. Runs within each `/tune-setup`, or when Orlando asks.

Read every memory file of every type (`user`, `feedback`, `project`, `reference`) and its index entry in:

- `~/.claude/projects/<span class="ph">&lt;project-memory-folder&gt;</span>/memory/` (workspace memory, loads in every `~/ai` session)
- `~/.claude/agent-memory/<span class="ph">&lt;name&gt;</span>/` for each agent (loads for that agent)

None: say so and stop.

One action each:
- **Keep as memory:** a preference or lesson working where it is.
- **Promote to `~/.claude/CLAUDE.md`:** applies to all work everywhere.
- **Promote to `~/ai/CLAUDE.md`:** applies to every agent in the workspace.
- **Promote to an agent definition** (`~/.claude/agents/<span class="ph">&lt;name&gt;</span>.md`): a standing rule for one agent&#x27;s role.
- **Promote to a skill:** belongs to a workflow that runs only when invoked.
- **Turn into a hook:** must hold every time and can be checked mechanically.
- **Merge** with a memory saying the same thing.
- **Delete:** one-off, stale, or already covered.

A memory repeating a rule already in a CLAUDE.md or agent file means the prose rule isn&#x27;t working: say so and consider a hook or clearer wording, not another copy.

Default to the narrowest place that fully covers the lesson. Every CLAUDE.md or memory index line costs context every session, so lean toward merging and deleting.

Show only the memories you&#x27;d change, grouped by action, one line each with location and reason, then one line for how many stay. Wait for my input before changing anything; then apply what we agreed. A promoted lesson: delete the memory and its index line so it isn&#x27;t loaded twice. Git history in `~/.claude` records everything removed.
</code></pre>

</figure>

</section>

<nav class="page-foot">

Previous: <a href="/ai-setup/skills/close-shop/">Close shop</a>.
Next: <a href="/ai-setup/skills/save-session/">Save session</a>.

<a href="/ai-setup/skills/">Back to Skills</a>

<a href="https://www.linkedin.com/in/oneillo/" target="_blank" rel="noopener">Borrowed something? Tell me how it went</a>

Text: <a href="https://creativecommons.org/licenses/by/4.0/" target="_blank" rel="license noopener">CC BY 4.0</a>. Scripts: <a href="https://opensource.org/license/mit" target="_blank" rel="license noopener">MIT</a>.

</nav>
