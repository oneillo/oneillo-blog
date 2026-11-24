---
title: "Rules"
date: 2026-11-22
lastmod: 2026-11-22
description: "AI-written: the rules files in Orlando's AI setup, the instructions every session starts from."
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

Rules come in layers. The global rules apply to every session, anywhere on the Mac. The workspace rules apply in the agent workspace, the folder where agents do their work. The knowledge base has its own rules file, for agents working in the notes.

- <a href="/ai-setup/rules/global/">Global rules</a>: A short global rules file that every Claude Code session and agent reads first.
- <a href="/ai-setup/rules/workspace/">Workspace rules</a>: The shared rules file for a personal AI workspace.
- <a href="/ai-setup/rules/knowledge-base/">Knowledge base rules</a>: The rules file for agents working in a personal notes vault.

## Project rules and context files

Each project adds its own rules file, and a context file that holds the project's goal, constraints, and decisions. A session reads them before it starts, so it can pick up where the last one stopped. They hold each project's private detail, so their text is not shared here.

## Memory

Claude Code keeps a memory for the workspace and one for each custom agent: lessons learned while working, and where things are. Memory is private by nature. It is never read for this section, and nothing here comes from it.
