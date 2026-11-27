---
title: "How work runs while I'm away: a walk-through of Orlando's Claude Code setup"
date: 2026-11-24
lastmod: 2026-11-27
summary: "AI-written: Shows how the keep-awake rule, the Relay skill, the Heartbeat skill, and the Latido app keep work going and report back while Orlando is away."
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

<div class="post-description">Shows how the keep-awake rule, the Relay skill, the Heartbeat skill, and the Latido app keep work going and report back while Orlando is away.</div>

<p class="ai-setup-label">Written by AI from Orlando's setup. Last updated <time datetime="2026-11-27">2026-11-27</time>. <a href="/ai-setup/#how-this-section-is-made">How this is made</a>.</p>

When Orlando steps away, several pieces of his setup keep work moving. Each has a different job.

## Staying awake

The Global rules say what happens first. When he steps away, works over Remote Control, or a job runs unattended, the session keeps the Mac awake without being asked. It wraps the long job in <code>caffeinate &#45;ims</code>, or starts one background <code>caffeinate</code> sized to the work. The screen may sleep. A session stops only the <code>caffeinate</code> it started.

The same rules say what to do when he is away from a session that isn't on Remote Control: suggest <code>&#47;remote&#45;control</code>. While it is on, the session sends a push notification when work is ready for his feedback or blocked on him. Routine progress never gets one.

## Long plans: the Relay skill

The Relay skill runs a plan as a chain of fresh sessions. The plan is a <code>roadmap&#46;md</code> cut into batches. One fresh session leads one batch. It briefs up to three builders, merges their work, runs the project's checks, has a fresh reviewer read the result, delivers, writes it up, and starts the next session.

Nothing waits for Orlando. His decisions sit in the roadmap with recommendations, and those are followed unless he has answered. State lives in the roadmap, <code>context&#46;md</code>, and git, so a batch interrupted by a power cut or usage limit is picked up from the files. Each lead also keeps the Mac awake with its own <code>caffeinate</code>.

## Scheduled jobs: the Heartbeat skill

The Heartbeat skill manages jobs that run on a schedule. Each job is one file that says when it runs, what the agent should do, and which tools and folders it may use. A job can also have a cheap check. The check decides whether there is any work, so an agent session starts only when there is.

One job is a relay watchdog. It restarts a relay that is switched on if its lead has gone away.

## Finding out what happened

The Latido app shows what the heartbeat is doing: its jobs, when they run, how each run ended, and whether anything is wrong. Its page says nothing more than that.

For a relay, the record is the roadmap's Log and git. A relay's status can also be read at any time, and each lead ends with a short message saying what he can now do and what waits for his decision.

## How they fit

The Heartbeat skill starts scheduled work, and Orlando starts a relay. The keep-awake rule keeps the Mac running while that work goes on. Latido, the roadmap's Log, and the push notifications are how he finds out what happened when he is back.

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
