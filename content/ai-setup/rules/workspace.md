---
title: "Workspace rules"
date: 2026-11-22
lastmod: 2026-11-22
description: "AI-written: the shared rules file for a personal AI workspace."
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

It tells every agent session how to work with its owner, how the workspace folders are laid out, how to start a project, how to keep version history, and how to handle feedback and memory.

**Works with:** <a href="/ai-setup/skills/close-shop/">Close shop</a>, <a href="/ai-setup/apps/#corillo">Corillo</a>, <a href="/ai-setup/agents/financial-advisor/">Financial advisor</a>, <a href="/ai-setup/skills/heartbeat/">Heartbeat</a>, <a href="/ai-setup/skills/process-feedback/">Process feedback</a>, <a href="/ai-setup/skills/save-session/">Save session</a>, <a href="/ai-setup/agents/trainer/">Trainer</a>, <a href="/ai-setup/skills/tune-setup/">Tune setup</a>, and <a href="/ai-setup/agents/writer/">Writer</a>.

## <code>CLAUDE&#46;md</code>

The workspace rules file that every agent session reads first. It sets how agents should behave, where things live, how projects start, how git is used, and where feedback and memory go.

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere"># Shared Agent Guidelines

These apply to every session in ~/ai/, including the custom agents (`writer`, `trainer`, `financial-advisor`) defined in `~/.claude/agents/`.

## How to Work With Me

- Push back when you think I&#x27;m wrong. Don&#x27;t agree just to be agreeable. If I&#x27;m proposing something that doesn&#x27;t make sense, say so directly with your reasoning before executing. The same goes for what&#x27;s already in place: if part of the setup no longer earns its keep, say so.
- Keep the setup simple. I want the best results for the least complexity. Every rule, skill, file and automation must earn its place; propose removing things as readily as adding them. Models and tools improve fast, so treat past setup decisions as provisional.
- If you don&#x27;t know something, say so. Never fill gaps with plausible-sounding guesses. Getting it wrong is worse than admitting uncertainty.
- Don&#x27;t state quantitative claims, technical judgments, or narrative details as fact unless you can verify them from available data. If you&#x27;re uncertain, say what you observe and let me confirm.
- When context files are loaded at session start, mine them for constraints and existing patterns before proposing or asking. Don&#x27;t ask questions already answered by the data.
- Use parallel subagents for independent research or reviews across several areas. Do small lookups yourself.
- Anything you want me to review (a plan, a draft, findings) opens in Corillo by default, not as a file path or pasted in chat. When I&#x27;m on my phone over Remote Control, don&#x27;t open or create documents in Corillo: I&#x27;m not at the computer, so it doesn&#x27;t help. Share Markdown I should read by copying it into `knowledge-base/_scratch/`, as the &quot;Scratch&quot; part of `knowledge-base/AGENTS.md` says. I read it in Obsidian and type my feedback at the bottom. At the Mac, review stays in Corillo.
- `~/ai` is my agent workspace. Inside it, do what the work needs without asking me first. That includes answering Claude Code&#x27;s &quot;trust this folder&quot; question for a folder you made here yourself. Bring me in when something reaches outside this folder (`~/.claude` stays as my global instructions say). The rules a project sets for itself, such as clean-room fences, still hold, and so do the ask-first lines in this file (a new area under `projects/`, leftover edits another session may own).

## Workspace Layout

- `knowledge-base/`: Orlando&#x27;s durable knowledge, a link to his Obsidian vault, split into areas. Before reading or writing there, follow `knowledge-base/AGENTS.md`; start from `knowledge-base/index.md` and the area hubs. Read what the task needs; don&#x27;t preload. Project-specific, short-lived context stays in the project folder; at `/close-shop`, anything a project produced that will stay true moves into the knowledge base.
- `projects/&lt;area&gt;/`: working output by area. Dated subfolders (`yyyymmdd-slug/`) for discrete pieces of work. When a project is finished, move it into a `zzCompleted/` folder inside its own area, keeping its folder name. File by subject: a project that builds a tool goes in the area the tool serves, and once the tool is in regular use it moves to top-level `tools/`, and keeps being improved there. `misc/` is the fallback for projects that fit no other area. Keep this level small and intentional: don&#x27;t add an area without asking Orlando, and nest deeper rather than spreading out.
- `tools/`: finished, reusable tools for the AI setup. Standalone scripts go in `tools/scripts/`. Larger tools get their own subfolder (for example, a backup tool that runs on a timer and has a restore runbook); a tool with its own git repo must be listed in `.gitignore` so the workspace repo doesn&#x27;t absorb it.
- `tmp/`: scratch space for throwaway files (intermediate outputs, test conversions, downloads you only need briefly). Ephemeral, not committed, not backed up to the cloud. Use it instead of a project folder or the system temp dir for anything that isn&#x27;t a deliverable. Anything there can be deleted at any time: a daily heartbeat job (`sweep-scratch`) removes what is untouched for 7 days, the vault&#x27;s `_scratch/` too. Never keep anything vital in either; originals live in project folders.

## Starting or Resuming a Project

When Orlando says we&#x27;re working on a project that already has a folder, your first action, before any other reading or work, is to check for a `context.md` at the folder&#x27;s root and read it if it exists. It holds that project&#x27;s goal, constraints and decisions.

When Orlando says we&#x27;re working on a project and no folder for it exists, create one at `projects/&lt;area&gt;/yyyymmdd-slug/` (today&#x27;s date, short hyphenated slug) before producing output. Pick the area that fits the work; use `misc/` if none does. Check for an existing folder first (including the area&#x27;s `zzCompleted/`) so you don&#x27;t duplicate one. Tell Orlando the path you created.

Every project folder gets a `context.md` at its root, created with the folder. Use the format in the `save-session` skill (`~/.claude/skills/save-session/SKILL.md`) and fill in what&#x27;s known at the start: the goal, constraints, and first steps. When Orlando sets a constraint or makes a decision, add it to `context.md` right away so it survives compaction; `/save-session` does the full update at the end of a session.

## Version History

- The workspace, the Claude Code config (agents, skills, memory) and the knowledge base each keep a local git history. Agents handle git; Orlando never runs it.
- Commit at `/close-shop`: this session&#x27;s changes first, then leftover edits once Orlando confirms no other session is working on them.
- Before a bulk change (moving, renaming, or deleting many files, or rewriting several config files at once), commit the current state as a checkpoint first so it can be undone in one step.
- These three stay local: never push them. A repo with its own remote is pushed only when I ask or one of my skills says so. Rewrite history only when I ask. Keep keys and passwords out of every commit. To undo something, confirm with Orlando, then restore the specific files rather than resetting the repo.
- Delete with `trash`, not `rm`, so a mistake can be recovered from the Trash (`rm` is fine inside `tmp/`). Before deleting something you didn&#x27;t create, check that nothing references it.

## Feedback and Memory

- Corrections go to auto memory as `feedback` memories, not to a separate log. `/process-feedback` reviews them at each `/tune-setup`.
- Bugs and ideas for my apps and tools go in a `feedback.md` at the root of that app&#x27;s or tool&#x27;s folder (for example `tools/&lt;app&gt;/feedback.md`), the moment I mention one: newest first, the date, what I saw or want in my words, what I was doing, and a status (open, in batch N, done in build X). Make the file with the first item. The next batch or session for that app takes its work from there and marks what it built. This is separate from feedback memories, which are about how you work with me.
- Memory, the workspace&#x27;s and each agent&#x27;s, holds lessons on how to work with me, where things are, and what was decided. What is true about me (profile, plans, programs, portfolios, series) goes in the knowledge base, with at most a one-line pointer in memory. An agent that finds such a fact in its memory offers to move it, with my OK, at its next real session with me.
</code></pre>

Taken out: 2 lists of knowledge-base areas, 1 list of project areas, 3 example names and paths, 1 backup arrangement detail, and 1 cloud storage name.
