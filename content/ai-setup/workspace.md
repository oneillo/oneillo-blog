---
title: "How it's organized: Orlando's Claude Code workspace"
date: 2026-11-22
lastmod: 2026-11-27
summary: "AI-written: how Orlando's AI workspace is organized: its folders, the knowledge base, version history, backups, and Python scripts."
heading: "How it's organized"
linkTitle: "How it's organized"
author: "Orlando O'Neill"
images: ["/ai-setup/cards/workspace.png"]
ShowReadingTime: false
ShowShareButtons: false
disableShare: true
ShowPostNavLinks: false
ShowBreadCrumbs: true
ShowWordCount: false
hideMeta: true
madeBy: "setup-share"
---

<div class="post-description">How Orlando's AI workspace is organized: its folders, the knowledge base, version history, backups, and Python scripts.</div>

<p class="ai-setup-label">Written by AI from Orlando's setup. Last updated <time datetime="2026-11-27">2026-11-27</time>. Also changed: <time datetime="2026-11-22">2026-11-22</time>. <a href="/ai-setup/#how-this-section-is-made">How this is made</a>.</p>

This page shows how Orlando organizes his Claude Code workspace, in plain words, for anyone who wants to build their own: where work lives, how history is kept, how it is backed up, and how scripts run.

## The workspace

One folder holds everything. It has the knowledge base, projects sorted by area in dated folders that each have a context file, finished tools, and a scratch folder. The scratch folder is swept daily and never holds anything vital, because the originals always live in a project folder.

## The knowledge base

The knowledge base is a set of notes with a clear shape. It has areas with a hub note each, and concept notes that link to their sources. It also has a scratch folder for notes he should review. Agents start from its rules file and its index. When a project closes, the facts that will stay true move into the knowledge base.

## Version history with git

The workspace, the Claude Code config folder, and the vault are local git repos with no remote. Agents do all the git work. There is a commit at the end of each session and a checkpoint before a bulk change. Undoing means restoring files, never resetting a repo. Why: any change an agent makes can be seen and undone.

## Backups

His reason, in his words: "If an agent ever accidentally removes a file completely, I can generally recover it from the cloud storage's recycle bin online."

The backup is one of three safety nets:

1. Deletes go to the Trash, not away for good.
2. Local version history in git keeps every committed state.
3. A cloud copy keeps the files off the Mac.

What is backed up: the workspace folder (<code>&#126;&#47;ai&#47;</code>), the Claude Code config folder (<code>&#126;&#47;&#46;claude&#47;</code>), and the blog's working folder, without its git history because that lives on GitHub. The vault's files can be mirrored too, and its git history is kept inside the workspace copy. A small snapshot of the things that live outside those folders (timers, shell setup, installed packages, and tool links) rides along. The cloud copy is OneDrive. A timer runs the backup every 30 minutes, and the copy mirrors the Mac, deletions included. Safeguards: it refuses to run if the cloud folder is missing, it warns when a run would delete an unusually large number of files, it warns when a git history disappears, and it reports an outage that lasts.

How a restore works, in plain steps you could follow to build your own:

1. Sign in to the cloud storage and the other accounts on the new Mac, and wait for the backup to download fully.
2. Install the tools: a package manager, Claude Code, <code>uv</code>, and Obsidian.
3. Copy the folders back from the cloud copy, without deleting anything.
4. Reconnect the vault from its own cloud sync, and rebuild any missing notes from the git history.
5. Restore the environment from the snapshot, and rebuild his own apps.
6. Check that every git repo answers.
7. Do a dry run of what the backup would delete, and stop if the list is long.
8. Load the backup timer last, because it starts mirroring the moment it is loaded.

A private GitHub repo or similar is another way to do this, with one catch: it has no recycle bin for a file deleted between commits, so it needs frequent automatic commits.

## Python with uv

Every script runs with <code>uv run</code> and declares its Python version and packages at its top. There are no virtual environments and no <code>pip</code>. Why: a script carries what it needs, nothing is installed system-wide, there is nothing to set up or clean up, and an agent can write and run a one-off script safely. Here is an example of the header:

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere"># /// script
# requires-python = &quot;&gt;=3.12&quot;
# dependencies = [&quot;requests&quot;]
# ///
</code></pre>

The same rules appear as text on the <a href="/ai-setup/rules/global/">Global rules</a> and <a href="/ai-setup/rules/workspace/">Workspace rules</a> pages.

<nav class="page-foot">

<a href="/ai-setup/">Back to AI Setup</a>

<a href="https://www.linkedin.com/in/oneillo/" target="_blank" rel="noopener">Borrowed something? Tell me how it went</a>

Text: <a href="https://creativecommons.org/licenses/by/4.0/" target="_blank" rel="license noopener">CC BY 4.0</a>. Scripts: <a href="https://opensource.org/license/mit" target="_blank" rel="license noopener">MIT</a>.

</nav>
