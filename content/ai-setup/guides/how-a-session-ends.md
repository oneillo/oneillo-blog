---
title: "How a session ends: a walk-through of Orlando's Claude Code setup"
date: 2026-11-24
lastmod: 2026-11-27
summary: "AI-written: Shows how a session saves project state, turns corrections into lasting lessons, links new notes, and commits, so the next session starts clean."
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

<div class="post-description">Shows how a session saves project state, turns corrections into lasting lessons, links new notes, and commits, so the next session starts clean.</div>

<p class="ai-setup-label">Written by AI from Orlando's setup. Last updated <time datetime="2026-11-27">2026-11-27</time>. <a href="/ai-setup/#how-this-section-is-made">How this is made</a>.</p>

When a working session ends, nothing learned should be lost. In this setup, Orlando types <code>&#47;close&#45;shop</code>, and the Close shop skill runs the whole routine in three steps. Each step leans on habits set in the Workspace rules, the file every session in the workspace reads first.

## Step 1: Save where the project stands

The Close shop skill first looks at each project the session changed. For each one, it follows the Save session skill. That skill reviews the conversation and writes or updates a <code>context&#46;md</code> file at the project's root. The file holds the goal, the constraints, the current state, key decisions, next steps, and open questions.

The Save session skill keeps the file short. It merges with what is already there, keeps earlier decisions, and rewrites the current state each time instead of stacking dated updates. It also checks the files, not memory, before saying something is done. If the session was only conversation, the step is skipped, and the skill says why.

The Workspace rules set this habit. They say every project folder gets a <code>context&#46;md</code>, and that decisions go into it right away so they survive a long session. <code>&#47;save&#45;session</code> then does the full update at the end.

Next, the Close shop skill moves anything that will stay true after the project ends into the knowledge base. Then it follows the Connect skill. That skill links the new or changed notes to the concept notes they discuss. It also creates a concept note when an idea now shows up across several sources, and it adds links between areas.

## Step 2: Turn corrections into lessons

The Close shop skill scans the session for moments Orlando corrected the approach or repeated himself. Each lesson that would change how an agent works goes into auto memory as a feedback memory.

Later, the Process feedback skill reviews those memories. It suggests one action for each: keep it, promote it to a rules file, agent, or skill, merge it, or delete it. It waits for Orlando's input before changing anything.

## Step 3: Commit

Last, the Close shop skill commits the session's changes in the workspace, the Claude Code config folder, and the knowledge base. It adds files by exact path and never pushes. The Workspace rules require this, so no finished work is left uncommitted.

## What you get

A project folder with a current <code>context&#46;md</code>, new knowledge filed and linked, corrections kept as lessons, and a clean commit history. The next session reads the context file first and knows where to pick up.

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
