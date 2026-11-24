---
title: "Rules: the Claude Code rules files in Orlando's setup"
date: 2026-11-22
lastmod: 2026-11-22
summary: "AI-written: the rules files in Orlando's AI setup, the instructions every session starts from."
heading: "Rules"
linkTitle: "Rules"
author: "Orlando O'Neill"
images: ["/ai-setup/cards/rules.png"]
ShowReadingTime: false
ShowShareButtons: false
disableShare: true
ShowPostNavLinks: false
ShowBreadCrumbs: true
ShowWordCount: false
hideMeta: true
madeBy: "setup-share"
---

<div class="post-description">The rules files in Orlando's AI setup, the instructions every session starts from.</div>

<p class="ai-setup-label">Written by AI from Orlando's setup. Last updated <time datetime="2026-11-22">2026-11-22</time>. <a href="/ai-setup/#how-this-section-is-made">How this is made</a>.</p>

Rules come in layers. The global rules apply to every session, anywhere on the Mac. The workspace rules apply in the agent workspace, the folder where agents do their work. The knowledge base has its own rules file, for agents working in the notes.

<div class="piece-grid">

- <a href="/ai-setup/rules/global/">Global rules</a>: Sets global rules for every session: direct style, keeping the Mac awake, file defaults, cleanup, Python with uv, and formatting.
- <a href="/ai-setup/rules/workspace/">Workspace rules</a>: Sets the shared rules for every session in a workspace: how to work together, where files live, how projects start, and how history is kept.
- <a href="/ai-setup/rules/knowledge-base/">Knowledge base rules</a>: Sets the rules agents follow to find, write, link, and check notes in a knowledge base kept as a vault of Markdown notes.

</div>

<div class="rules-table">

## Where each file lives

| File | Where it lives | When a session reads it |
|---|---|---|
| <a href="/ai-setup/rules/global/">Global rules</a> | `~/.claude/CLAUDE.md` | Every Claude Code session reads it at the start. |
| <a href="/ai-setup/rules/workspace/">Workspace rules</a> | `<your-workspace>/CLAUDE.md` | Every Claude Code session started in the workspace, or in a folder inside it, reads it at the start. |
| <a href="/ai-setup/rules/knowledge-base/">Knowledge base rules</a> | In the knowledge base's own folder | Sessions read it before they work in the knowledge base. |

</div>

## Project rules and context files

Each project adds its own rules file, and a context file that holds the project's goal, constraints, and decisions. A session reads them before it starts, so it can pick up where the last one stopped. They hold each project's private detail, so their text is not shared here.

## Memory

Claude Code keeps a memory for the workspace and one for each custom agent: lessons learned while working, and where things are. Memory is private by nature. It is never read for this section, and nothing here comes from it.
