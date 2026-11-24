---
title: "How to borrow from Orlando's Claude Code setup"
date: 2026-11-24
lastmod: 2026-11-24
summary: "AI-written: how to take a rule, skill, agent, or hook from Orlando's AI setup and make it work in your own Claude Code."
heading: "How to borrow"
linkTitle: "How to borrow"
author: "Orlando O'Neill"
images: ["/ai-setup/cards/borrow.png"]
ShowReadingTime: false
ShowShareButtons: false
disableShare: true
ShowPostNavLinks: false
ShowBreadCrumbs: true
ShowWordCount: false
hideMeta: true
madeBy: "setup-share"
---

<div class="post-description">How to take a rule, skill, agent, or hook from Orlando's AI setup and make it work in your own Claude Code.</div>

<p class="ai-setup-label">Written by AI from Orlando's setup. Last updated <time datetime="2026-11-24">2026-11-24</time>. <a href="/ai-setup/#how-this-section-is-made">How this is made</a>.</p>

Every rule, skill, agent, and hook here is a plain text file. To borrow one, copy it into the right place in your own Claude Code setup, then make it yours.

## Where each file goes

- **Global rules:** `~/.claude/CLAUDE.md`
- **Workspace rules:** `<your-workspace>/CLAUDE.md`
- **A skill:** `~/.claude/skills/<name>/SKILL.md`, its other files beside it
- **A custom agent:** `~/.claude/agents/<name>.md`
- **A hook:** `~/.claude/hooks/<file>`, set up in `~/.claude/settings.json`

## Try one skill safely

1. Read the whole page first, scripts too. Each one is one person's working setup, not a supported tool.
2. Make a throwaway folder and put the skill in it, at `.claude/skills/<name>/SKILL.md`. There it works only for sessions started in that folder.
3. Put your own values where you see placeholders like `<project-folder>`.
4. Start a session in that folder, type `/<name>`, and watch what it does.
5. Once it does what you want, move it to `~/.claude/skills/<name>/`.

## Hand it to your agent

Give your agent the page's address and paste this:

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere">Read this page and adapt it to my setup. Ask me for each placeholder.
</code></pre>

<a href="/ai-setup/">Back to AI Setup</a>

<a href="https://www.linkedin.com/in/oneillo/" target="_blank" rel="noopener">Borrowed something? Tell me how it went</a>

Text: <a href="https://creativecommons.org/licenses/by/4.0/" target="_blank" rel="license noopener">CC BY 4.0</a>. Scripts: <a href="https://opensource.org/license/mit" target="_blank" rel="license noopener">MIT</a>.
