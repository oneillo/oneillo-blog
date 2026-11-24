---
title: "Orlando's Claude Code setup: rules, skills, agents, and hooks"
date: 2026-11-22
lastmod: 2026-11-24
summary: "An AI-written tour of Orlando's personal AI setup: how it's organized, rules, skills, agents, apps, and settings."
heading: "AI Setup"
linkTitle: "AI Setup"
author: "Orlando O'Neill"
images: ["/ai-setup/cards/ai-setup.png"]
ShowReadingTime: false
ShowShareButtons: false
disableShare: true
ShowPostNavLinks: false
ShowBreadCrumbs: true
ShowWordCount: false
hideMeta: true
madeBy: "setup-share"
---

<div class="post-description">A tour of Orlando's personal AI setup: how it's organized, rules, skills, agents, apps, and settings.</div>

<aside class="ai-box"><p><strong>Written by AI.</strong> This page was generated from Orlando's own setup by an automated run, and checked for private details before it was published. <a href="/ai-setup/#how-this-section-is-made">How this section is made</a>. Last updated <time datetime="2026-11-24">2026-11-24</time>.</p></aside>

<section class="block what">

## What this is

This section is for people, and their agents, who want to borrow from a working AI setup. Every text here can be copied and adapted.

It is one person's setup, Orlando's, built around Claude Code on a Mac. It is not a product.

It changes often. Every page says when its own content last changed.

</section>

<figure class="ai-setup-diagram">
<style>.ai-setup-diagram{margin:1.5rem 0;position:relative}.ai-setup-diagram picture,.ai-setup-diagram img{display:block;width:100%;height:auto;margin:0}.ai-setup-diagram .ai-setup-diagram-dark{display:none}[data-theme="dark"] .ai-setup-diagram .ai-setup-diagram-light{display:none}[data-theme="dark"] .ai-setup-diagram .ai-setup-diagram-dark{display:block}@media (prefers-color-scheme:dark){[data-theme="auto"] .ai-setup-diagram .ai-setup-diagram-light{display:none}[data-theme="auto"] .ai-setup-diagram .ai-setup-diagram-dark{display:block}}.ai-setup-diagram-text ul{margin-top:.5rem}.ai-setup-diagram .ai-setup-diagram-links a{position:absolute;display:block;border-radius:10px;box-shadow:none;text-decoration:none}.ai-setup-diagram .ai-setup-diagram-links a:hover,.ai-setup-diagram .ai-setup-diagram-links a:focus-visible{outline:2px solid currentColor;outline-offset:1px}.ai-setup-diagram .ai-setup-diagram-narrow{display:none}@media (max-width:600px){.ai-setup-diagram .ai-setup-diagram-wide{display:none}.ai-setup-diagram .ai-setup-diagram-narrow{display:block}}</style>
<picture class="ai-setup-diagram-light">
<source media="(max-width: 600px)" srcset="diagram-narrow-light.svg" width="360" height="1148">
<img src="diagram-wide-light.svg" alt="How Orlando&#x27;s AI setup fits together: an AI-written diagram. The same, as text, is in the fold below it." width="720" height="839" loading="lazy" decoding="async">
</picture>
<picture class="ai-setup-diagram-dark">
<source media="(max-width: 600px)" srcset="diagram-narrow-dark.svg" width="360" height="1148">
<img src="diagram-wide-dark.svg" alt="How Orlando&#x27;s AI setup fits together: an AI-written diagram. The same, as text, is in the fold below it." width="720" height="839" loading="lazy" decoding="async">
</picture>
<div class="ai-setup-diagram-links ai-setup-diagram-wide">
<a href="/ai-setup/workspace/" style="left:0.139%;top:82.837%;width:99.722%;height:16.687%" aria-label="How it&#x27;s organized" title="How it&#x27;s organized"></a>
<a href="/ai-setup/apps/" style="left:0.139%;top:11.442%;width:45.694%;height:25.149%" aria-label="Apps" title="Apps"></a>
<a href="/ai-setup/skills/" style="left:2.778%;top:55.662%;width:45.833%;height:19.07%" aria-label="Skills" title="Skills"></a>
<a href="/ai-setup/agents/" style="left:51.389%;top:55.662%;width:45.833%;height:19.07%" aria-label="Custom agents" title="Custom agents"></a>
<a href="/ai-setup/skills/heartbeat/" style="left:56.111%;top:15.971%;width:41.667%;height:5.125%" aria-label="Heartbeat" title="Heartbeat"></a>
<a href="/ai-setup/skills/relay/" style="left:56.111%;top:24.791%;width:41.667%;height:5.125%" aria-label="Relay" title="Relay"></a>
<a href="/ai-setup/apps/#corillo" style="left:1.944%;top:15.971%;width:20%;height:8.701%" aria-label="Corillo" title="Corillo"></a>
<a href="/ai-setup/apps/#brega" style="left:23.056%;top:15.971%;width:20%;height:8.701%" aria-label="Brega" title="Brega"></a>
<a href="/ai-setup/rules/" style="left:2.778%;top:47.08%;width:31.111%;height:5.244%" aria-label="Rules" title="Rules"></a>
<a href="/ai-setup/settings/" style="left:66.944%;top:47.08%;width:30.556%;height:5.244%" aria-label="Settings and hooks" title="Settings and hooks"></a>
<a href="/ai-setup/apps/#latido" style="left:23.056%;top:26.579%;width:20%;height:6.913%" aria-label="Latido" title="Latido"></a>
<a href="/ai-setup/apps/#pregon" style="left:1.944%;top:26.579%;width:20%;height:5.125%" aria-label="Pregón" title="Pregón"></a>
<a href="/ai-setup/agents/financial-advisor/" style="left:74.167%;top:62.336%;width:18.333%;height:3.337%" aria-label="Financial advisor" title="Financial advisor"></a>
<a href="/ai-setup/agents/trainer/" style="left:63.75%;top:62.336%;width:9.306%;height:3.337%" aria-label="Trainer" title="Trainer"></a>
<a href="/ai-setup/agents/writer/" style="left:53.889%;top:62.336%;width:8.75%;height:3.337%" aria-label="Writer" title="Writer"></a>
</div>
<div class="ai-setup-diagram-links ai-setup-diagram-narrow">
<a href="/ai-setup/workspace/" style="left:0.278%;top:79.443%;width:99.444%;height:20.209%" aria-label="How it&#x27;s organized" title="How it&#x27;s organized"></a>
<a href="/ai-setup/apps/" style="left:0.278%;top:6.969%;width:45.833%;height:27.7%" aria-label="Apps" title="Apps"></a>
<a href="/ai-setup/skills/" style="left:3.889%;top:55.226%;width:92.222%;height:11.15%" aria-label="Skills" title="Skills"></a>
<a href="/ai-setup/agents/" style="left:3.889%;top:67.247%;width:92.222%;height:7.317%" aria-label="Custom agents" title="Custom agents"></a>
<a href="/ai-setup/rules/" style="left:3.333%;top:48.258%;width:44.444%;height:6.098%" aria-label="Rules" title="Rules"></a>
<a href="/ai-setup/apps/#corillo" style="left:2.778%;top:10.105%;width:38.889%;height:6.098%" aria-label="Corillo" title="Corillo"></a>
<a href="/ai-setup/skills/relay/" style="left:56.667%;top:15.418%;width:38.889%;height:6.098%" aria-label="Relay" title="Relay"></a>
<a href="/ai-setup/apps/#brega" style="left:2.778%;top:16.638%;width:38.889%;height:6.098%" aria-label="Brega" title="Brega"></a>
<a href="/ai-setup/apps/#latido" style="left:2.778%;top:28.484%;width:38.889%;height:4.878%" aria-label="Latido" title="Latido"></a>
<a href="/ai-setup/settings/" style="left:47.778%;top:48.258%;width:51.111%;height:3.484%" aria-label="Settings and hooks" title="Settings and hooks"></a>
<a href="/ai-setup/skills/heartbeat/" style="left:56.667%;top:10.105%;width:38.889%;height:3.659%" aria-label="Heartbeat" title="Heartbeat"></a>
<a href="/ai-setup/apps/#pregon" style="left:2.778%;top:23.171%;width:38.889%;height:3.659%" aria-label="Pregón" title="Pregón"></a>
<a href="/ai-setup/agents/financial-advisor/" style="left:46.111%;top:71.08%;width:36.111%;height:2.265%" aria-label="Financial advisor" title="Financial advisor"></a>
<a href="/ai-setup/agents/trainer/" style="left:26.389%;top:71.08%;width:18.056%;height:2.265%" aria-label="Trainer" title="Trainer"></a>
<a href="/ai-setup/agents/writer/" style="left:7.778%;top:71.08%;width:16.944%;height:2.265%" aria-label="Writer" title="Writer"></a>
</div>
</figure>
<details class="ai-setup-diagram-text">
<summary>The diagram as text</summary>
<ul>
<li>How Orlando&#x27;s AI setup fits together.</li>
<li>Orlando chats with a Claude Code session from the Mac or his phone.</li>
<li>Orlando uses his apps: Corillo (review documents with an agent), Brega (a to-do list shared with agents), Pregón (publish a post), and Latido (a window onto the heartbeat).</li>
<li>Corillo and Brega pass comments, edits, and tasks to a session and back.</li>
<li>While Orlando is away, the Heartbeat (jobs on a timer) and the Relay (a roadmap, one fresh session per batch) start sessions.</li>
<li>A session reads the rules at the start: global, workspace, and project.</li>
<li>Settings and hooks check each step a session takes.</li>
<li>A session can use 14 skills: close-shop, save-session, process-feedback, tune-setup, connect, obsidian-to-hugo, publish-prep, publish-post, publish-setup, podcast, video-title-cards, relay, heartbeat, and rex.</li>
<li>A session can work as one of 3 custom agents: writer, trainer, and financial advisor.</li>
<li>A session reads and writes the files on the Mac: the knowledge base (an Obsidian vault), projects (by area, in dated folders), tools (finished and reusable), and scratch (throwaway files).</li>
<li>The files are kept under local version history and backed up to the cloud.</li>
</ul>
</details>


<section class="block start">

## Start here

1. <a href="/ai-setup/rules/global/">Global rules</a>: short and general, the rules every session starts from.
2. <a href="/ai-setup/skills/save-session/">Save session</a> and <a href="/ai-setup/skills/close-shop/">Close shop</a>: the habit of a context file per project, so the next session picks up where the last one stopped.
3. <a href="/ai-setup/settings/#no-em-dash">No em dash</a>: a small hook with one clear job, stopping em dashes in anything an agent writes.

</section>

<section class="block goals">

## By what you want to do

- **Pick up where you left off:** <a href="/ai-setup/skills/save-session/">Save session</a>, <a href="/ai-setup/skills/close-shop/">Close shop</a>, and <a href="/ai-setup/rules/workspace/">Workspace rules</a>.
- **Let work run while you're away:** <a href="/ai-setup/skills/relay/">Relay</a>, <a href="/ai-setup/skills/heartbeat/">Heartbeat</a>, and <a href="/ai-setup/rules/global/">the keep-awake rule</a>.
- **Undo an agent's mistake:** version history, backups, and the Trash, all on <a href="/ai-setup/workspace/">How it's organized</a>.
- **Keep the setup lean:** <a href="/ai-setup/skills/process-feedback/">Process feedback</a> and <a href="/ai-setup/skills/tune-setup/">Tune setup</a>.
- **Write and publish:** <a href="/ai-setup/agents/writer/">Writer</a>, <a href="/ai-setup/skills/obsidian-to-hugo/">Obsidian to Hugo</a>, <a href="/ai-setup/skills/publish-prep/">Publish prep</a>, and <a href="/ai-setup/skills/publish-post/">Publish post</a>.

</section>

<section class="block pieces">

## The pieces

<div class="piece-grid">

- <a href="/ai-setup/workspace/">How it's organized</a>: the workspace's folders, the knowledge base's shape, version history, backups, and how Python scripts run.
- <a href="/ai-setup/rules/">Rules</a>: the rules every session starts from (3 rules files).
- <a href="/ai-setup/skills/">Skills</a>: written routines a session can run (14 skills).
- <a href="/ai-setup/agents/">Custom agents</a>: roles a session can take, each with its own prompt (3 agents).
- <a href="/ai-setup/apps/">Apps</a>: the apps around the sessions, described (6 apps).
- <a href="/ai-setup/settings/">Settings and hooks</a>: the switches underneath (2 hooks).

</div>

Orlando's weekly AI digest is public too, as the <a href="/newsletter/">Newsletter</a>.

</section>

<section class="block guides">

## Walk-throughs

Each one follows a job across several pieces, and links to each.

- <a href="/ai-setup/guides/how-a-session-ends/">How a session ends</a>: Shows how a session ends: it saves project state, files lasting knowledge, keeps corrections, and commits, so the next session starts clean.
- <a href="/ai-setup/guides/while-im-away/">How work runs while I'm away</a>: Shows how the keep-awake rule, the Relay and Heartbeat skills, and the Latido app keep work going while Orlando is away, and how he sees the results.
- <a href="/ai-setup/guides/three-safety-nets/">Three safety nets</a>: Shows the three ways Orlando's setup undoes an agent's mistake: the Trash, local version history, and the cloud copy, and when each one helps.

</section>

<section class="block runs">

## What this setup runs on

A Mac, Claude Code, Rex (the terminal app), Obsidian (the knowledge base), Hugo (the blog), uv (for Python scripts), git (for version history), and ffmpeg (for audio and video).

</section>

<section class="block made">

## How this section is made

1. Orlando starts a publish when he wants one.
2. The run gathers his rules, skills, and agents from his Mac, from a fixed list of places.
3. An AI model takes out anything private and writes the short descriptions.
4. A separate check holds back any page where something private is left: that page is shown as described only, or left out until the next publish.
5. The pages are built. At first Orlando looks at what changed and says yes before it goes live.

Nobody edits the AI-written text by hand. A note marked as Orlando's is his own words. A description may be wrong where the text under it is right: the text is the source.

</section>

<section class="block borrow">

## Borrowing from it

Every text here can be copied and made your own. <a href="/ai-setup/borrow/">How to borrow</a> says where each file goes, how to try one safely, and what to ask your own agent.

</section>

<section class="block updated">

## Last updated

2026-11-24. Added the How to borrow page, the What changed page, the How a session ends page, the Three safety nets page, and the How work runs while I'm away page. Changed 1 page.

<a href="/ai-setup/changes/">What changed</a> lists every publish, newest first.

## Follow changes

<a href="/ai-setup/changes/">What changed</a> lists every publish, newest first, with the pieces each one added, changed, or removed.

The section's <a href="/ai-setup/index.xml">feed</a> lists only its main pages, each when it first comes out. It doesn't list each rule, skill, or agent page, or say when a page changes.

</section>
