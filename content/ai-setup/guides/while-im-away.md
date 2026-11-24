---
title: "How work runs while I'm away: a walk-through of Orlando's Claude Code setup"
date: 2026-11-24
lastmod: 2026-11-24
summary: "AI-written: Shows how the keep-awake rule, the Relay and Heartbeat skills, and the Latido app keep work going while Orlando is away, and how he sees the results."
heading: "How work runs while I'm away"
linkTitle: "How work runs while I'm away"
author: "Orlando O'Neill"
images: ["/ai-setup/cards/guides/while-im-away.png"]
ShowReadingTime: false
ShowShareButtons: false
disableShare: true
ShowPostNavLinks: false
ShowBreadCrumbs: true
ShowWordCount: false
hideMeta: true
madeBy: "setup-share"
---

<div class="post-description">Shows how the keep-awake rule, the Relay and Heartbeat skills, and the Latido app keep work going while Orlando is away, and how he sees the results.</div>

<p class="ai-setup-label">Written by AI from Orlando's setup. Last updated <time datetime="2026-11-24">2026-11-24</time>. <a href="/ai-setup/#how-this-section-is-made">How this is made</a>.</p>

Work can keep going when nobody is at the computer. Four pieces make that happen, and each does a different job.

## Keeping the Mac awake

The Global rules file is read by every session and agent. One rule covers time away: when Orlando says he is stepping away, works over Remote Control, or a job runs unattended, the session keeps the Mac awake without being asked. It does this with <code>caffeinate</code>, either around the long job or as one background timer sized to the work. The screen may sleep. The session stops only what it started.

## Running a plan

The Relay skill runs a plan as a chain of fresh sessions. The plan lives in a project's <code>roadmap&#46;md</code>, cut into batches. One session, called the lead, takes one batch. It briefs builders, brings their work together, runs the project's checks, has a fresh reviewer read the result, and writes it up. Then it starts the next lead and ends.

A fresh session per batch keeps cost down, because no session carries a growing pile of context. The state lives in the roadmap, <code>context&#46;md</code>, and git, so a relay can be stopped and started anywhere, even after a crash or a usage limit. Each lead also starts its own <code>caffeinate</code>. Orlando can pause it by editing the Controls at the top of the roadmap.

## Running on a schedule

The Heartbeat skill adds jobs that run at set times. Each job is one file: when it runs, a free check for whether there is any work, and a checklist for an agent. The agent starts only if the check finds work, and it runs with limits on its tools, its folders, its time, and its cost. One job is a relay watchdog. If a relay that is switched on loses its lead, the watchdog can start a new one.

## Seeing what happened

The Latido app shows what the heartbeat is doing, in a small window.

When Orlando comes back, he has several places to look:

- The roadmap's Log and git hold what each finished batch did.
- <code>&#45;&#45;status</code> on the relay's start script says where a relay stands.
- <code>heartbeat log</code> shows recent job runs and what they cost.
- <code>heartbeat doctor</code> names anything that needs him.
- Latido shows the heartbeat at a glance.

## The pieces it joins

- <a href="/ai-setup/skills/relay/">Relay</a>
- <a href="/ai-setup/skills/heartbeat/">Heartbeat</a>
- <a href="/ai-setup/rules/global/">Global rules</a>
- <a href="/ai-setup/apps/#latido">Latido</a>

<nav class="page-foot">

<a href="/ai-setup/">Back to AI Setup</a>

<a href="https://www.linkedin.com/in/oneillo/" target="_blank" rel="noopener">Borrowed something? Tell me how it went</a>

Text: <a href="https://creativecommons.org/licenses/by/4.0/" target="_blank" rel="license noopener">CC BY 4.0</a>. Scripts: <a href="https://opensource.org/license/mit" target="_blank" rel="license noopener">MIT</a>.

</nav>
