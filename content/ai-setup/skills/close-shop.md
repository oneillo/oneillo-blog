---
title: "Close shop"
date: 2026-11-22
lastmod: 2026-11-22
description: "AI-written: an end-of-session routine that an agent runs when work is done."
ShowReadingTime: false
ShowShareButtons: false
disableShare: true
ShowPostNavLinks: false
ShowBreadCrumbs: true
ShowWordCount: false
hideMeta: true
madeBy: "setup-share"
---

> **Written by AI.** This page was generated from Orlando's own setup by an automated run, and checked for private details before it was published. <a href="/ai-setup/#how-this-section-is-made">How this section is made</a>. Last updated 2026-11-22.

It saves each project's context, checks that corrections from the session are stored as memory, and commits only the session's own changes to the local git repos.

**Works with:** <a href="/ai-setup/skills/connect/">Connect</a>, <a href="/ai-setup/agents/financial-advisor/">Financial advisor</a>, <a href="/ai-setup/skills/process-feedback/">Process feedback</a>, <a href="/ai-setup/skills/save-session/">Save session</a>, <a href="/ai-setup/agents/trainer/">Trainer</a>, <a href="/ai-setup/skills/tune-setup/">Tune setup</a>, and <a href="/ai-setup/agents/writer/">Writer</a>.

## <code>SKILL&#46;md</code>

The skill that runs at the end of a work session. It saves project notes, records lessons in memory, and commits the session's changes in three steps.

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

Then promote durable knowledge. If the project produced anything that will stay true after the project ends (facts about Orlando, reference material, finished notes), move it into the knowledge base at `&lt;vault-folder&gt;/` (copy only when the project still needs the file, and say so), following its `AGENTS.md`, and run `uv run ~/ai/tools/scripts/kb.py check` on what you added. Then note in `context.md` what moved and where it lives now. Then connect it: follow `~/.claude/skills/connect/SKILL.md` for the notes you added or changed, so they link to the concepts they discuss and new recurring ideas get concept notes. Say in one line what you moved and connected, or that nothing qualified.

## Step 2: Check corrections are in memory

Scan the session for moments Orlando corrected the approach, had to repeat himself, or confirmed a non-obvious approach worked. For each lesson that would change how an agent works in a future session, make sure auto memory has it as a `feedback` memory (in the session&#x27;s memory directory, following the auto memory format), and save it if not. Skip one-off slips and anything already in a CLAUDE.md, agent file or memory. Say in one line what you saved, or that nothing qualified. `/process-feedback` reviews them at each `/tune-setup`.

## Step 3: Commit this session&#x27;s changes

`~/ai/`, `~/.claude/`, and the knowledge base (`&lt;vault-folder&gt;/`, its history kept in `&lt;vault-history-folder&gt;/`) are local git repos with no remote. Orlando never runs git; you do it.

For each of the three repos:

1. Run `git -C &lt;repo&gt; status --short`.
2. Stage only the files this session created, edited, or deleted, including the `context.md` and memory changes from Steps 1 and 2. Stage by explicit path (`git -C &lt;repo&gt; add -- &lt;paths&gt;`). Never `git add -A` or `git add .` at the repo root.
3. Commit only those paths (`git -C &lt;repo&gt; commit -m &quot;&lt;subject&gt;&quot; -- &lt;paths&gt;`), with a subject of `&lt;agent&gt;: &lt;what changed&gt;` (agent is the name of the custom agent that ran the session, or `main` for a plain session), under 72 characters. Add a short body with bullets for the main changes and the project folder, if any.
4. If nothing from this session changed in a repo, skip it.

If the session changed files in a folder that has its own repo (listed in `~/ai/.gitignore`), commit there by that repo&#x27;s own rules, never with `git add -f` from `~/ai`, and name that repo in the closing line. If that repo&#x27;s own rules say to ask about pushing, ask Orlando.

Then handle leftovers: changes this session didn&#x27;t make. These are usually Orlando&#x27;s own edits or another session&#x27;s, and left alone they sit uncommitted for days. If any remain in any of the three, list them and ask Orlando once whether another session is still working on them. If not, add and commit them by explicit path (`-- &lt;paths&gt;`, as above) with the subject `unattributed: &lt;short summary&gt;`. If one is, leave those files alone.

Never push (the question above is the one exception), amend, rebase, reset, or otherwise rewrite history. Never commit secrets; if a file looks like one, stop and ask.

Finish by telling Orlando, in one line per repo, what was committed (or that nothing was), plus anything left uncommitted and why.
</code></pre>

Taken out: 3 paths, and 1 list of agent names.
