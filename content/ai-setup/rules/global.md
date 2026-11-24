---
title: "Global rules: a Claude Code rules file that sets house rules for every session"
date: 2026-11-22
lastmod: 2026-11-22
summary: "AI-written: Sets global rules for every session: direct style, keeping the Mac awake, file defaults, cleanup, Python with uv, and formatting."
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

<div class="post-description">Sets global rules for every session: direct style, keeping the Mac awake, file defaults, cleanup, Python with uv, and formatting.</div>

> **Written by AI.** This page was generated from Orlando's own setup by an automated run, and checked for private details before it was published. <a href="/ai-setup/#how-this-section-is-made">How this section is made</a>. Last updated 2026-11-22.

- **What it is:** Global Steering is a Claude Code global rules file that sets how every session and agent talks, handles files, cleans up, and runs Python.
- **How it starts:** Every Claude Code session reads it at the start.
- **Where the file goes:** <code>&#126;&#47;&#46;claude&#47;CLAUDE&#46;md</code>
- **Needs:** uv, Rex, and macOS

**Works with:** <a href="/ai-setup/skills/rex/">Rex</a>.

## The rules file (<code>CLAUDE&#46;md</code>) {#claude-md}

<a href="#claude-md">Link to this file</a>

The global rules file read by every Claude Code session and agent, covering style, keeping the Mac awake, file naming, cleanup, Python scripts, and formatting.

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere"># Global Steering

These rules apply to all Claude Code sessions and agents.

## Interaction Style

- Be direct. Lead with the answer or action, not the reasoning.
- Skip preamble, filler phrases, and trailing summaries. If I can read the diff, don&#x27;t restate it.
- No compliment sandwiches. If something is wrong, say so clearly.
- Use plain language. Explain things like a smart colleague, not a textbook.

## Keeping the Mac Awake

- When I say I&#x27;m stepping away, when I work with you over Remote Control, or when a job runs unattended, keep the Mac awake without being asked. Wrap the long job (`caffeinate -ims &lt;command&gt;` lasts exactly as long as it runs), or start one `caffeinate -ims -t &lt;seconds&gt;` in the background sized to the work, unless one is already running. The display may sleep: Remote Control doesn&#x27;t need it. Stop only what you started.
- While the front desk (`~/ai/tools/scripts/front-desk.sh`, in its own Rex tab) runs, it already keeps the Mac awake: start nothing more.
- When I step away and the session isn&#x27;t on Remote Control, suggest `/remote-control`. While it is, send a push notification when work is ready for my feedback or blocked on me, and each time a reply ends with a question for me. Never for routine progress.

## File Defaults

- Unless I ask otherwise, create files in markdown format.
- Use descriptive, lowercase, hyphenated filenames (e.g., `my-topic-notes.md`).

## Filesystem Boundaries

- Read and write within `~/ai/` and `~/.claude/` only, unless I explicitly direct you elsewhere.
- Do not create files outside these directories without asking first.

## Cleaning Up

- Clean up after yourself, and brief every agent you start to do the same. Whatever a task creates that isn&#x27;t the deliverable (temp folders, test data, caches, builds, downloads, worktrees, background processes) is removed when the task ends. Tests remove what they create as they finish.
- Leftovers that grow with use are a leak, the same as a memory leak in an app. This covers the system temp folder (`$TMPDIR`), not just the project folder.
- Before long, repeated or parallel runs, check what each run leaves behind and where, check free disk space (`df -h`), and put a cleanup or cap in place. Check free space again at milestones.

## Python

- Use `uv run` to execute Python scripts. Do not create virtual environments or use pip directly.
- Always include inline script metadata declaring dependencies and Python version:
  ```
  # /// script
  # requires-python = &quot;&gt;=3.12&quot;
  # dependencies = [&quot;requests&quot;]
  # ///
  ```
- Omit the `dependencies` field if the script only uses the standard library.

## Formatting

- Prefer short, direct sentences.
- Use headers and lists to organize multi-part responses, but don&#x27;t over-structure short answers.
- No emojis unless I ask for them.
- Use commas, periods, colons, or parentheses where an em dash might go. Em dashes read as AI-written, in chat and in files.
</code></pre>
