---
title: "Close shop: a Claude Code skill that ends a working session in order"
date: 2026-11-22
lastmod: 2026-11-27
summary: "AI-written: Ends a working session in order: saves project context, checks corrections are in memory, and commits the session's changes."
heading: "Close shop"
linkTitle: "Close shop"
author: "Orlando O'Neill"
images: ["/ai-setup/cards/skills/close-shop.png"]
ShowReadingTime: false
ShowShareButtons: false
disableShare: true
ShowPostNavLinks: false
ShowBreadCrumbs: true
ShowWordCount: false
hideMeta: true
madeBy: "setup-share"
---

<div class="post-description">Ends a working session in order: saves project context, checks corrections are in memory, and commits the session's changes.</div>

<p class="ai-setup-label">Written by AI from Orlando's setup. Last updated <time datetime="2026-11-27">2026-11-27</time>. Also changed: <time datetime="2026-11-22">2026-11-22</time>. <a href="/ai-setup/#how-this-section-is-made">How this is made</a>.</p>

<section class="facts facts-skill">

<div class="facts-head"><span class="tag tag-skill">Skill</span><span class="facts-cmd"><code>/close-shop</code></span></div>

- **What it is:** Close shop is a Claude Code skill that ends a working session in three ordered steps.
- **How it starts:** You type <code>&#47;close&#45;shop</code>. Claude doesn't start it by itself.
- **Where the file goes:** <code>&#126;&#47;&#46;claude&#47;skills&#47;close&#45;shop&#47;SKILL&#46;md</code>
- **Needs:** git and uv
- **Taken out for privacy:** 1 list of permissions, 2 paths, and 1 detail of how history is kept.

<div class="works">

**Works with:**
- **Called by:** <a href="/ai-setup/rules/workspace/">Workspace rules</a>.

</div>

</section>

<section class="file">

## The skill file (<code>SKILL&#46;md</code>) {#skill-md}

<a href="#skill-md">Link to this file</a>

The main file of the skill. It sets out the three steps run at the end of a session: save project context, check that corrections are in memory, and commit the session's changes.

<figure class="code">
<div class="code-bar"><span class="code-name">SKILL.md</span><span class="code-meta">Markdown · 46 lines</span><button type="button" class="ai-setup-copy copy" hidden>Copy</button></div>

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere">---
name: close-shop
description: Runs the end-of-session routine: saves project context, checks corrections are in memory, and commits this session&#x27;s changes. Use when Orlando types /close-shop or asks to close shop.
disable-model-invocation: true
[A list of things the skill may do is left out here.]
---

# Close Shop

Three steps, in order.

## Step 1: Save session

For each project the session changed (a folder under `~/ai/projects/`, a tool under `~/ai/tools/`, or any folder with a `context.md`), read `~/.claude/skills/save-session/SKILL.md` and follow it. Skip if no project files were read, edited or created, or the session was only conversation or brainstorming; say why in one line.

Then promote durable knowledge: anything that will stay true after the project ends (facts about Orlando, reference material, finished notes).

1. Move it into `~/ai/knowledge-base/` following its `AGENTS.md` (copy only when the project still needs the file, and say so).
2. Run `uv run ~/ai/tools/scripts/kb.py check` on what you added.
3. Note in `context.md` what moved and where it lives now.
4. Follow `~/.claude/skills/connect/SKILL.md` for the notes added or changed.

Say in one line what you moved and connected, or that nothing qualified.

## Step 2: Check corrections are in memory

Scan the session for moments Orlando corrected the approach, repeated himself, or confirmed a non-obvious approach worked. Each lesson that would change how an agent works in a future session must be in auto memory as a `feedback` memory (session&#x27;s memory directory, auto memory format); save it if not. Skip one-off slips and anything already in a CLAUDE.md, agent file or memory. Say in one line what you saved, or that nothing qualified.

## Step 3: Commit this session&#x27;s changes

The workspace, the Claude Code config folder, and the knowledge base are local git repos. Orlando never runs git; you do.

For each of the three:

1. `git -C <span class="ph">&lt;repo&gt;</span> status --short`.
2. Stage only files this session created, edited or deleted, including Step 1 and 2 `context.md` and memory changes, by explicit path (`git -C <span class="ph">&lt;repo&gt;</span> add -- <span class="ph">&lt;paths&gt;</span>`). Never `git add -A` or `git add .` at the root.
3. `git -C <span class="ph">&lt;repo&gt;</span> commit -m &quot;<span class="ph">&lt;subject&gt;</span>&quot; -- <span class="ph">&lt;paths&gt;</span>`. Subject `<span class="ph">&lt;agent&gt;</span>: <span class="ph">&lt;what changed&gt;</span>` (`writer`, `trainer`, `financial-advisor`, or `main` for a plain session), under 72 characters. Short body: bullets for the main changes and the project folder, if any.
4. Skip a repo this session didn&#x27;t change.

A folder with its own repo (listed in the workspace&#x27;s ignore file): commit there by that repo&#x27;s own rules, never `git add -f` from the workspace, and name it in the closing line. If its rules say to ask about pushing, ask Orlando.

Leftovers (changes this session didn&#x27;t make, usually Orlando&#x27;s or another session&#x27;s; left alone they sit uncommitted for days): if any remain in the three repos, list them and ask Orlando once whether another session is still working on them. If not, commit them by explicit path with subject `unattributed: <span class="ph">&lt;short summary&gt;</span>`. If one is, leave those files alone.

Never push (the own-repo push question is the one exception), amend, rebase, reset or otherwise rewrite history. Never commit anything that looks like a secret; if a file looks like one, stop and ask.

Finish with one line per repo to Orlando: what was committed (or nothing), plus anything left uncommitted and why.
</code></pre>

</figure>

</section>

<nav class="page-foot">

Next: <a href="/ai-setup/skills/process-feedback/">Process feedback</a>.

<a href="/ai-setup/skills/">Back to Skills</a>

<a href="https://www.linkedin.com/in/oneillo/" target="_blank" rel="noopener">Borrowed something? Tell me how it went</a>

Text: <a href="https://creativecommons.org/licenses/by/4.0/" target="_blank" rel="license noopener">CC BY 4.0</a>. Scripts: <a href="https://opensource.org/license/mit" target="_blank" rel="license noopener">MIT</a>.

</nav>
