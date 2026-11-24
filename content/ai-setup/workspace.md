---
title: "How it's organised"
date: 2026-11-22
lastmod: 2026-11-22
description: "AI-written: how Orlando's AI workspace is organised: its folders, the knowledge base, version history, backups, and Python scripts."
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

This page explains how Orlando's AI setup is organised: one workspace folder, a knowledge base, local version history, a timed cloud backup, and Python scripts that run with uv. It is described only, so no original text is shared.

Everything lives in one workspace folder, and the sections below cover the knowledge base, version history, backups, and how scripts are run.

## The workspace

One folder holds everything. It has the knowledge base, projects sorted by area, finished tools, and a scratch folder. Each project lives in a dated folder, and each of those has a context file that records its goal, constraints, and decisions. When a project is finished, it moves to a completed folder inside its area. Finished, reusable tools move to a top-level tools folder and keep being improved there. The scratch folder is swept daily, so it never holds anything vital: originals live in project folders.

## The knowledge base

The knowledge base is a vault of notes, split into areas. Each area has a hub note, and concept notes link back to their sources. A scratch folder inside it holds notes he should review. Agents start from its rules file and its index, and read only what the task needs. When a project closes, anything it produced that will stay true moves into the knowledge base.

## Version history with git

The workspace, the Claude Code config folder, and the vault are each a local git repo with no remote. Agents do all the git work. There is a commit at the end of each session, and a checkpoint before any bulk change such as moving, renaming, or deleting many files. Undoing means restoring specific files. Why: any change an agent makes can be seen and undone.

## Backups

His reason, in his words: "If an agent ever accidentally removes a file completely, I can generally recover it from the cloud storage's recycle bin online."

The backup is one of three safety nets:

1. Deletes go to the Trash, not straight to oblivion, so a mistake can be recovered.
2. Local version history, kept in git.
3. A cloud copy, kept in OneDrive.

The backup copies the workspace folder and the Claude Code config folder to OneDrive (cloud storage). It runs every 30 minutes, on a timer. The copy mirrors deletions, which is why the recycle bin matters. It leaves out scratch files, built apps, and installed packages. It also does not include session transcripts, which are dropped on purpose.

A restore works in plain steps:

1. Sign in to the cloud storage and let the backup folder download fully.
2. Reinstall the tools the setup needs.
3. Copy the folders back to their places, without deleting anything.
4. Reconnect the knowledge base, and rebuild any missing notes from its git history if needed.
5. Check that every git repo answers and has its history.
6. Do a dry run of the backup to see what it would delete, and stop if the list is long.
7. Only then turn the backup timer back on, so it does not start deleting the backup to match a half-restored machine.

A private GitHub repo or similar is another way to back up. It has one catch: it has no recycle bin for a file deleted between commits, so it needs frequent automatic commits.

## Python with uv

Every script runs with <code>uv run</code> and declares its Python version and packages at its top. There are no virtual environments and no <code>pip</code>. Why: a script carries what it needs, nothing is installed system-wide, there is nothing to set up or clean up, and an agent can write and run a one-off script safely.

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere"># /// script
# requires-python = &quot;&gt;=3.12&quot;
# dependencies = [&quot;requests&quot;]
# ///
</code></pre>

**Works with:** <a href="/ai-setup/apps/#brega">Brega</a>, <a href="/ai-setup/skills/close-shop/">Close shop</a>, <a href="/ai-setup/apps/#corillo">Corillo</a>, <a href="/ai-setup/agents/financial-advisor/">Financial advisor</a>, <a href="/ai-setup/skills/heartbeat/">Heartbeat</a>, <a href="/ai-setup/skills/process-feedback/">Process feedback</a>, <a href="/ai-setup/skills/rex/">Rex</a>, <a href="/ai-setup/skills/save-session/">Save session</a>, <a href="/ai-setup/agents/trainer/">Trainer</a>, <a href="/ai-setup/skills/tune-setup/">Tune setup</a>, and <a href="/ai-setup/agents/writer/">Writer</a>.

The same rules appear as text on the <a href="/ai-setup/rules/global/">Global rules</a> and <a href="/ai-setup/rules/workspace/">Workspace rules</a> pages.

Taken out: nothing.
