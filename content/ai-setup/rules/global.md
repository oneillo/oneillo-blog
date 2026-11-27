---
title: "Global rules: a Claude Code rules file that sets style and habits for every session"
date: 2026-11-22
lastmod: 2026-11-27
summary: "AI-written: Sets the style, file habits, cleanup duties, and Python rules that every Claude Code session and agent follows."
heading: "Global rules"
linkTitle: "Global rules"
author: "Orlando O'Neill"
images: ["/ai-setup/cards/rules/global.png"]
ShowReadingTime: false
ShowShareButtons: false
disableShare: true
ShowPostNavLinks: false
ShowBreadCrumbs: true
ShowWordCount: false
hideMeta: true
madeBy: "setup-share"
---

<div class="post-description">Sets the style, file habits, cleanup duties, and Python rules that every Claude Code session and agent follows.</div>

<p class="ai-setup-label">Written by AI from Orlando's setup. Last updated <time datetime="2026-11-27">2026-11-27</time>. Also changed: <time datetime="2026-11-22">2026-11-22</time>. <a href="/ai-setup/#how-this-section-is-made">How this is made</a>.</p>

<section class="facts facts-rule">

<div class="facts-head"><span class="tag tag-rule">Rules</span></div>

- **What it is:** The global steering file is a Claude Code rules file (global) that sets how every session and agent writes, keeps the Mac awake, handles files, cleans up, and runs Python.
- **How it starts:** Every Claude Code session reads it at the start.
- **Where the file goes:** <code>&#126;&#47;&#46;claude&#47;CLAUDE&#46;md</code>
- **Needs:** Rex, uv, and Chrome

<div class="works">

**Works with:**
- **Also mentions:** <a href="/ai-setup/skills/rex/">Rex</a>.

</div>

</section>

<section class="file">

## The rules file (<code>CLAUDE&#46;md</code>) {#claude-md}

<a href="#claude-md">Link to this file</a>

The global rules file that every Claude Code session reads. It sets the writing style, keeps the Mac awake during long jobs, and covers file habits, cleanup, the browser, and Python.

<figure class="code">
<div class="code-bar"><span class="code-name">CLAUDE.md</span><span class="code-meta">Markdown · 44 lines</span><button type="button" class="ai-setup-copy copy" hidden>Copy</button></div>

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere"># Global Steering

For all Claude Code sessions and agents.

## Style

- Be direct: lead with the answer or action. No preamble, filler, trailing summaries, or restating a diff I can read.
- No compliment sandwiches. If something is wrong, say so clearly.
- Plain language, like a smart colleague, not a textbook. Short, direct sentences.
- Headers and lists for multi-part answers; don&#x27;t over-structure short ones.
- No emojis unless I ask.
- No em dashes, in chat or files (they read as AI-written): use commas, colons, periods or parentheses.

## Keeping the Mac Awake

- When I step away, work with you over Remote Control, or a job runs unattended, keep the Mac awake without being asked: wrap the long job in `caffeinate -ims <span class="ph">&lt;command&gt;</span>` (lasts exactly as long as it runs), or start one background `caffeinate -ims -t <span class="ph">&lt;seconds&gt;</span>` sized to the work, unless one is already running. The display may sleep (Remote Control doesn&#x27;t need it). Stop only what you started.
- While the front desk (`~/ai/tools/scripts/front-desk.sh`, in its own Rex tab) runs with `--until`, it keeps the Mac awake: start nothing more.
- When I step away and the session isn&#x27;t on Remote Control, suggest `/remote-control`. While it is, send a push notification when work is ready for my feedback or blocked on me, and each time a reply ends with a question for me. Never for routine progress.

## Files

- Unless I ask otherwise: Markdown, with descriptive lowercase-hyphenated names (`my-topic-notes.md`).
- Read and write only within `~/ai/` and `~/.claude/` unless I direct otherwise. Ask before creating files elsewhere.

## Cleaning Up

- Clean up after yourself, and brief every agent you start to do the same. Whatever a task creates that isn&#x27;t the deliverable (temp folders, test data, caches, builds, downloads, worktrees, background processes) is removed when the task ends. Tests remove what they create as they finish.
- Leftovers that grow with use are a leak, like a memory leak, including in the system temp folder (`$TMPDIR`), not just the project folder.
- Before long, repeated or parallel runs: check what each run leaves behind and where, check free space (`df -h`), and put a cleanup or cap in place. Recheck free space at milestones.

## Browser

- Claude in Chrome is off by default. When a task needs the browser, start that session with `claude --chrome`.

## Python

- Run scripts with `uv run`. No virtual environments, no pip.
- Always include inline script metadata (omit `dependencies` if stdlib only):
  ```
  # /// script
  # requires-python = &quot;&gt;=3.12&quot;
  # dependencies = [&quot;requests&quot;]
  # ///
  ```
</code></pre>

</figure>

</section>

<nav class="page-foot">

Next: <a href="/ai-setup/rules/workspace/">Workspace rules</a>.

<a href="/ai-setup/rules/">Back to Rules</a>

<a href="https://www.linkedin.com/in/oneillo/" target="_blank" rel="noopener">Borrowed something? Tell me how it went</a>

Text: <a href="https://creativecommons.org/licenses/by/4.0/" target="_blank" rel="license noopener">CC BY 4.0</a>. Scripts: <a href="https://opensource.org/license/mit" target="_blank" rel="license noopener">MIT</a>.

</nav>
