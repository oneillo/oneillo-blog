---
title: "How a session ends: a walk-through of Orlando's Claude Code setup"
date: 2026-11-24
lastmod: 2026-11-28
summary: "AI-written: Shows how a session saves project context, checks corrections are in memory, links new notes, and commits before it ends."
heading: "How a session ends"
linkTitle: "How a session ends"
author: "Orlando O'Neill"
images: ["/ai-setup/cards/guides/how-a-session-ends.png"]
ShowReadingTime: false
ShowShareButtons: false
disableShare: true
ShowPostNavLinks: false
ShowBreadCrumbs: true
ShowWordCount: false
hideMeta: true
madeBy: "setup-share"
---

<div class="post-description">Shows how a session saves project context, checks corrections are in memory, links new notes, and commits before it ends.</div>

<p class="ai-setup-label">Written by AI from Orlando's setup. Last updated <time datetime="2026-11-28">2026-11-28</time>. <a href="/ai-setup/#how-this-section-is-made">How this is made</a>.</p>

A working session ends with one command: <code>&#47;close&#45;shop</code>. The Close shop skill runs three steps in order, so nothing learned is lost and the next session starts clean.

## Step 1: Save where the project stands

For each project the session changed, the Close shop skill follows the Save session skill. That skill writes a <code>context&#46;md</code> file at the root of the project folder. The file holds the goal, constraints, current state, key decisions, next steps, and open questions.

The skill keeps the file short, because a future session reads it at the start. It merges with any existing file, keeps earlier decisions, and rewrites the current state instead of stacking dated updates. It also checks the files before claiming something is done.

The Workspace rules set this habit. They say every project folder gets a <code>context&#46;md</code> when it is created. They also say to read it first when a project is resumed.

Next, the Close shop skill moves anything that will stay true after the project ends into the knowledge base. It records the move in <code>context&#46;md</code>. Then it follows the Connect skill for the notes it added or changed. That skill links each note to the concept notes it discusses. It also creates a concept note when an idea now recurs across sources.

## Step 2: Check corrections are in memory

The skill scans the session for moments when Orlando corrected the approach, repeated himself, or confirmed that a non-obvious approach worked. Each lesson that would change how an agent works goes into memory as a <code>feedback</code> memory. This follows the Workspace rules, which send corrections to memory instead of a separate log.

Later, the Process feedback skill (<code>&#47;process&#45;feedback</code>) reviews those memories. For each one, it suggests an action: keep it, merge it, delete it, or promote it to a rules file, an agent, a skill, or a hook. Nothing changes until Orlando agrees.

## Step 3: Commit

The workspace, the Claude Code config, and the knowledge base each keep a local git history. The Close shop skill commits this session's changes by explicit path, including the <code>context&#46;md</code> and memory changes. It never pushes. Changes from other sessions are listed, and Orlando is asked about them once.

## What you get

A project folder with a <code>context&#46;md</code> that tells the next session where to pick up, lessons saved in memory, and every change committed.

## The pieces it joins

- <a href="/ai-setup/skills/save-session/">Save session</a>
- <a href="/ai-setup/skills/close-shop/">Close shop</a>
- <a href="/ai-setup/skills/process-feedback/">Process feedback</a>
- <a href="/ai-setup/skills/connect/">Connect</a>
- <a href="/ai-setup/rules/workspace/">Workspace rules</a>

<nav class="page-foot">

<a href="/ai-setup/">Back to AI Setup</a>

<a href="https://www.linkedin.com/in/oneillo/" target="_blank" rel="noopener">Borrowed something? Tell me how it went</a>

Text: <a href="https://creativecommons.org/licenses/by/4.0/" target="_blank" rel="license noopener">CC BY 4.0</a>. Scripts: <a href="https://opensource.org/license/mit" target="_blank" rel="license noopener">MIT</a>.

</nav>
