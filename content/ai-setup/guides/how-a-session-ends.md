---
title: "How a session ends: a walk-through of Orlando's Claude Code setup"
date: 2026-11-24
lastmod: 2026-11-24
summary: "AI-written: Shows how a session ends: it saves project state, files lasting knowledge, keeps corrections, and commits, so the next session starts clean."
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

<div class="post-description">Shows how a session ends: it saves project state, files lasting knowledge, keeps corrections, and commits, so the next session starts clean.</div>

<p class="ai-setup-label">Written by AI from Orlando's setup. Last updated <time datetime="2026-11-24">2026-11-24</time>. <a href="/ai-setup/#how-this-section-is-made">How this is made</a>.</p>

A working session ends with a short routine, so nothing learned is lost. Orlando starts it with <code>&#47;close&#45;shop</code>. The Close shop skill runs three steps in order, and it calls on other skills along the way.

## Where the habits come from

The Workspace rules, which every session in the workspace loads, set the habits behind all of this. They say every project folder has a <code>context&#46;md</code> file, and that decisions go into it right away so they survive a long session. They also say the full update happens at the end, and that durable knowledge moves into the knowledge base when the session closes.

## Step 1: Save where the project stands

If the session changed a project, the Close shop skill hands over to the Save session skill. It finds the project folder, then creates or updates the <code>context&#46;md</code> file there. If the file exists, it reads it first and merges, so earlier decisions are kept. The file holds the goal, constraints, current state, key decisions, next steps, and open questions. The current state is rewritten each time, not stacked up with dated notes. The skill checks the files before it writes that something is done. If the session was only conversation, Close shop says so in one line and moves on.

Next, anything that will stay true after the project ends moves into the knowledge base. That includes facts, reference material, and finished notes. Close shop notes the move in <code>context&#46;md</code>. Then the Connect skill runs on the notes that were added or changed. It links them to the ideas they discuss, builds concept notes for ideas that now recur across sources, and adds links between areas. It finishes with checks on the knowledge base.

## Step 2: Keep the corrections

Close shop scans the session for moments when Orlando corrected the approach or had to repeat himself. Each lesson that would change how an agent works next time is saved as a <code>feedback</code> memory, unless it is already written down somewhere. Later, the Process feedback skill reviews those memories during a regular tune-up. It decides whether each one stays, moves into a rules file, an agent, or a skill, merges, or goes. Orlando approves before anything changes.

## Step 3: Commit

Close shop commits this session's changes in each of the three local repos, staging only the files it touched. It never pushes. It then lists any leftover changes it didn't make and asks Orlando before committing them.

## What you get

A project folder that tells the next session where to pick up, with knowledge filed, lessons saved, and the history committed.

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
