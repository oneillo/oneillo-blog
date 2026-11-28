---
title: "How the setup improves: a walk-through of Orlando's Claude Code setup"
date: 2026-11-28
lastmod: 2026-11-28
summary: "AI-written: Shows how corrections become lessons, reviewed and tested changes, and an approved update, so the setup improves without getting worse."
heading: "How the setup improves"
linkTitle: "How the setup improves"
author: "Orlando O'Neill"
images: ["/ai-setup/cards/guides/how-the-setup-improves.png"]
ShowReadingTime: false
ShowShareButtons: false
disableShare: true
ShowPostNavLinks: false
ShowBreadCrumbs: true
ShowWordCount: false
hideMeta: true
madeBy: "setup-share"
---

<div class="post-description">Shows how corrections become lessons, reviewed and tested changes, and an approved update, so the setup improves without getting worse.</div>

<p class="ai-setup-label">Written by AI from Orlando's setup. Last updated <time datetime="2026-11-28">2026-11-28</time>. <a href="/ai-setup/#how-this-section-is-made">How this is made</a>.</p>

This setup changes in a loop. Each step hands something to the next, and nothing replaces what runs now until Orlando says so.

## Corrections become lessons

It starts in an ordinary session. When Orlando corrects Claude, auto memory saves the correction as a <code>feedback</code> memory. It takes effect in the next session on its own. Over time these pile up, and every memory index line loads in every session, so they need regular review.

## The Process feedback skill reviews them

The Process feedback skill (<code>&#47;process&#45;feedback</code>) reads every saved memory, for the workspace and for each agent. It suggests one action for each: keep it as a memory, promote it to a rules file, an agent definition, or a skill, turn it into a hook, merge it with another, or delete it. It leans toward the narrowest place that covers the lesson, and toward merging and deleting. It shows only what it would change, waits for Orlando's input, and then applies what was agreed. The Tune setup skill runs it too.

## The Tune setup skill proposes changes

The Tune setup skill (<code>&#47;tune&#45;setup</code>) researches current Anthropic guidance and checks the rules, skills, agents, and settings against it. Its proposed memory changes join its other findings. It writes one proposals page, ranked by value, with what to change, why, the expected effect, and the risk. It applies nothing until Orlando approves specific proposals. It also says plainly when guidance conflicts with how he likes to work, and he decides.

## The Evals skill and tool check the change

Some proposals, such as a changed agent, a new model or effort, or an edited instruction file, can be tested before they go live. The Evals skill runs the Evals tool, which checks that a change is no worse than the current version, using blind judging by three judges. Both versions answer the same cases. The judges compare the answers, and Orlando gets one page with the verdict.

The skill has firm limits. It never proposes a change by itself, because a check starts only after Orlando and a session have decided on a change. It never edits the current file. It also works on a copy kept outside any folder Claude Code loads, so the candidate can't go live early.

## Orlando's OK puts it in place

The verdict is information, not a decision. Whether the candidate replaces the current version is Orlando's call, in his own words. Until then, nothing changes. Once he agrees, the change goes in.

## The pieces it joins

- <a href="/ai-setup/skills/process-feedback/">Process feedback</a>
- <a href="/ai-setup/skills/tune-setup/">Tune setup</a>
- <a href="/ai-setup/skills/evals/">Evals</a>
- <a href="/ai-setup/evals/">Evals</a>

<nav class="page-foot">

<a href="/ai-setup/">Back to AI Setup</a>

<a href="https://www.linkedin.com/in/oneillo/" target="_blank" rel="noopener">Borrowed something? Tell me how it went</a>

Text: <a href="https://creativecommons.org/licenses/by/4.0/" target="_blank" rel="license noopener">CC BY 4.0</a>. Scripts: <a href="https://opensource.org/license/mit" target="_blank" rel="license noopener">MIT</a>.

</nav>
