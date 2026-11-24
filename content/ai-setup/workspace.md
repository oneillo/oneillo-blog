---
title: "How it's organized: Orlando's Claude Code workspace"
date: 2026-11-22
lastmod: 2026-11-22
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

<p class="ai-setup-label">Written by AI from Orlando's setup. Last updated <time datetime="2026-11-22">2026-11-22</time>. <a href="/ai-setup/#how-this-section-is-made">How this is made</a>.</p>

This page explains how Orlando's setup is organized: one workspace for agents, a knowledge base, local git history, an automatic cloud backup, and Python scripts that carry their own needs.

## The workspace

One folder holds everything. It has the knowledge base, projects sorted by area in dated folders that each have a context file, finished tools, and a scratch folder. The scratch folder is swept daily and never holds anything vital.

## The knowledge base

The knowledge base is a vault of notes. It is split into areas, and each area has a hub note. Concept notes link back to their sources. A scratch folder holds notes for Orlando to review. Agents start from the vault's rules file and its index. When a project closes, the lasting facts move into the knowledge base.

## Version history with git

The workspace, the Claude Code config folder, and the vault are each a local git repo with no remote. Agents do all the git work. There is a commit at the end of each session and a checkpoint before a bulk change. Undoing means restoring files. Why: any change an agent makes can be seen and undone.

## Backups

"If an agent ever accidentally removes a file completely, I can generally recover it from the cloud storage's recycle bin online."

The backup is one of three safety nets:

1. Deletes go to the Trash, so a mistake can be recovered from there.
2. Local version history keeps every committed change.
3. The cloud copy mirrors the workspace and the Claude Code config folder.

His backup goes to OneDrive (cloud storage). It copies <code>&#126;&#47;ai&#47;</code> and <code>&#126;&#47;&#46;claude&#47;</code> every 30 minutes, on a timer. A private GitHub repo or similar is another way to do it, with one catch: it has no recycle bin for a file deleted between commits, so it needs frequent automatic commits.

To restore, follow these steps:

1. Sign in to the cloud storage on the new computer and let the backup folder download fully.
2. Install the tools the setup needs, such as Claude Code and uv.
3. Copy the backed-up folders back to <code>&#126;&#47;ai&#47;</code> and <code>&#126;&#47;&#46;claude&#47;</code>, without deleting anything.
4. Check that every git repo answers and still has its history.
5. Do a dry run of the backup to see what it would delete, and stop if the list is long.
6. Turn the backup timer back on last, so it never mirrors a half-restored computer over the backup.

## Python with uv

Every script runs with <code>uv run</code> and declares its Python version and packages at its top. There are no virtual environments and no <code>pip</code>. Why: a script carries what it needs, nothing is installed system-wide, there is nothing to set up or clean up, and an agent can write and run a one-off script safely.

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
