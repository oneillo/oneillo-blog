---
title: "Close shop: a Claude Code skill that ends a working session in order"
date: 2026-11-22
lastmod: 2026-11-22
summary: "AI-written: Ends a working session in three steps: saves project context, checks corrections are in memory, and commits the session's changes."
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

<div class="post-description">Ends a working session in three steps: saves project context, checks corrections are in memory, and commits the session's changes.</div>

<p class="ai-setup-label">Written by AI from Orlando's setup. Last updated <time datetime="2026-11-22">2026-11-22</time>. <a href="/ai-setup/#how-this-section-is-made">How this is made</a>.</p>

<section class="facts facts-skill">

<div class="facts-head"><span class="tag tag-skill">Skill</span><span class="facts-cmd"><code>/close-shop</code></span></div>

- **What it is:** Close shop is a Claude Code skill that ends a working session by saving project context, checking memory, and committing changes.
- **How it starts:** You type <code>&#47;close&#45;shop</code>. Claude doesn't start it by itself.
- **Where the file goes:** <code>&#126;&#47;&#46;claude&#47;skills&#47;close&#45;shop&#47;SKILL&#46;md</code>
- **Needs:** git
- **To make it yours:** Put your own values where you see <code>&#60;vault&#45;history&#45;folder&#62;</code>.
  Taken out for privacy: 1 path.

<div class="works">

**Works with:**
- **Calls:** <a href="/ai-setup/skills/process-feedback/">Process feedback</a> and <a href="/ai-setup/skills/tune-setup/">Tune setup</a>.
- **Called by:** <a href="/ai-setup/rules/workspace/">Workspace rules</a>.

</div>

</section>

<section class="file">

## The skill file (<code>SKILL&#46;md</code>) {#skill-md}

<a href="#skill-md">Link to this file</a>

The skill's instructions for the end of a working session, covering saving project context, saving corrections to memory, and committing the session's changes to local git repos.

<figure class="code">
<div class="code-bar"><span class="code-name">SKILL.md</span><span class="code-meta">Markdown · 41 lines</span><button type="button" class="ai-setup-copy copy" hidden>Copy</button></div>

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere">---
name: close-shop
description: End-of-session routine that saves project context, checks corrections are saved to memory, and commits this session&#x27;s changes
disable-model-invocation: true
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, AskUserQuestion
---

# Close Shop

Run the end-of-session routine in three steps, in order.

## Step 1: Save session

Save for each project the session changed: a folder under `~/ai/projects/`, a tool under `~/ai/tools/`, or any folder that already has a `context.md`. Skip if no project files were read, edited, or created, or the session was just conversation or brainstorming. If skipping, say why in one line and move on.

Otherwise, read `~/.claude/skills/save-session/SKILL.md` and follow it.

Then promote durable knowledge. If the project produced anything that will stay true after the project ends (facts about Orlando, reference material, finished notes), move it into the knowledge base at `~/ai/knowledge-base/` (copy only when the project still needs the file, and say so), following its `AGENTS.md`, and run `uv run ~/ai/tools/scripts/kb.py check` on what you added. Then note in `context.md` what moved and where it lives now. Then connect it: follow `~/.claude/skills/connect/SKILL.md` for the notes you added or changed, so they link to the concepts they discuss and new recurring ideas get concept notes. Say in one line what you moved and connected, or that nothing qualified.

## Step 2: Check corrections are in memory

Scan the session for moments Orlando corrected the approach, had to repeat himself, or confirmed a non-obvious approach worked. For each lesson that would change how an agent works in a future session, make sure auto memory has it as a `feedback` memory (in the session&#x27;s memory directory, following the auto memory format), and save it if not. Skip one-off slips and anything already in a CLAUDE.md, agent file or memory. Say in one line what you saved, or that nothing qualified. `/process-feedback` reviews them at each `/tune-setup`.

## Step 3: Commit this session&#x27;s changes

`~/ai/`, `~/.claude/`, and the knowledge base (`~/ai/knowledge-base/`, its history kept in `<span class="ph">&lt;vault-history-folder&gt;</span>`) are local git repos with no remote. Orlando never runs git; you do it.

For each of the three repos:

1. Run `git -C <span class="ph">&lt;repo&gt;</span> status --short`.
2. Stage only the files this session created, edited, or deleted, including the `context.md` and memory changes from Steps 1 and 2. Stage by explicit path (`git -C <span class="ph">&lt;repo&gt;</span> add -- <span class="ph">&lt;paths&gt;</span>`). Never `git add -A` or `git add .` at the repo root.
3. Commit only those paths (`git -C <span class="ph">&lt;repo&gt;</span> commit -m &quot;<span class="ph">&lt;subject&gt;</span>&quot; -- <span class="ph">&lt;paths&gt;</span>`), with a subject of `<span class="ph">&lt;agent&gt;</span>: <span class="ph">&lt;what changed&gt;</span>` (agent is `writer`, `trainer`, `financial-advisor`, or `main` for a plain session), under 72 characters. Add a short body with bullets for the main changes and the project folder, if any.
4. If nothing from this session changed in a repo, skip it.

If the session changed files in a folder that has its own repo (listed in `~/ai/.gitignore`), commit there by that repo&#x27;s own rules, never with `git add -f` from `~/ai`, and name that repo in the closing line. If that repo&#x27;s own rules say to ask about pushing, ask Orlando.

Then handle leftovers: changes this session didn&#x27;t make. These are usually Orlando&#x27;s own edits or another session&#x27;s, and left alone they sit uncommitted for days. If any remain in any of the three, list them and ask Orlando once whether another session is still working on them. If not, add and commit them by explicit path (`-- <span class="ph">&lt;paths&gt;</span>`, as above) with the subject `unattributed: <span class="ph">&lt;short summary&gt;</span>`. If one is, leave those files alone.

Never push (the question above is the one exception), amend, rebase, reset, or otherwise rewrite history. Never commit secrets; if a file looks like one, stop and ask.

Finish by telling Orlando, in one line per repo, what was committed (or that nothing was), plus anything left uncommitted and why.
</code></pre>

</figure>

</section>

<nav class="page-foot">

Next: <a href="/ai-setup/skills/process-feedback/">Process feedback</a>.

<a href="/ai-setup/skills/">Back to Skills</a>

<a href="https://www.linkedin.com/in/oneillo/" target="_blank" rel="noopener">Borrowed something? Tell me how it went</a>

Text: <a href="https://creativecommons.org/licenses/by/4.0/" target="_blank" rel="license noopener">CC BY 4.0</a>. Scripts: <a href="https://opensource.org/license/mit" target="_blank" rel="license noopener">MIT</a>.

</nav>
