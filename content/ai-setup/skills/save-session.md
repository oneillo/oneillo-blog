---
title: "Save session: a Claude Code skill that saves project context for the next session"
date: 2026-11-22
lastmod: 2026-11-22
summary: "AI-written: Saves where a project stands in a context file so the next session can pick up where this one left off."
heading: "Save session"
linkTitle: "Save session"
author: "Orlando O'Neill"
images: ["/ai-setup/cards/skills/save-session.png"]
ShowReadingTime: false
ShowShareButtons: false
disableShare: true
ShowPostNavLinks: false
ShowBreadCrumbs: true
ShowWordCount: false
hideMeta: true
madeBy: "setup-share"
---

<div class="post-description">Saves where a project stands in a context file so the next session can pick up where this one left off.</div>

<p class="ai-setup-label">Written by AI from Orlando's setup. Last updated <time datetime="2026-11-22">2026-11-22</time>. <a href="/ai-setup/#how-this-section-is-made">How this is made</a>.</p>

<section class="facts facts-skill">

<div class="facts-head"><span class="tag tag-skill">Skill</span><span class="facts-cmd"><code>/save-session</code></span></div>

- **What it is:** Save session is a Claude Code skill that writes a project's goal, decisions, and next steps into a context file at the end of a working session.
- **How it starts:** You type <code>&#47;save&#45;session</code>. Claude doesn't start it by itself.
- **Where the file goes:** <code>&#126;&#47;&#46;claude&#47;skills&#47;save&#45;session&#47;SKILL&#46;md</code>
- **Needs:** Nothing extra
- **To make it yours:** Put your own values where you see <code>&#60;project&#45;folder&#62;</code>.
  Taken out for privacy: 1 private project folder pattern.

<div class="works">

**Works with:**
- **Called by:** <a href="/ai-setup/rules/workspace/">Workspace rules</a>.

</div>

</section>

<section class="file">

## The skill file (<code>SKILL&#46;md</code>) {#skill-md}

<a href="#skill-md">Link to this file</a>

The instructions for saving a project's context at the end of a working session. It tells Claude how to find the project folder and write or update a context file there.

<figure class="code">
<div class="code-bar"><span class="code-name">SKILL.md</span><span class="code-meta">Markdown · 61 lines</span><button type="button" class="ai-setup-copy copy" hidden>Copy</button></div>

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere">---
name: save-session
description: Save project context at end of a working session for continuity in future sessions
disable-model-invocation: true
allowed-tools: Read, Write, Edit, Glob, Grep, Bash
---

# Save Session Context

Review the full conversation and save a structured context file so a future session can pick up where this one left off.

## Determine the project folder

Identify the project the user has been working on this session. Look at:
- The files that were read, edited, or created during the conversation
- The working directory and any directory changes
- The user&#x27;s explicit mentions of a project name or folder

The project folder is the root directory of whatever project the user was working on (e.g., `~/ai/projects/<span class="ph">&lt;area&gt;</span>/<span class="ph">&lt;project-folder&gt;</span>/`, `~/ai/tools/<span class="ph">&lt;tool&gt;</span>/`). If you cannot confidently determine the project folder, ask the user before proceeding.

## Write context.md

Create or update a `context.md` file in the root of the project folder.

Use this structure:

```markdown
---
name: [project name]
description: [one-line project description]
last-updated: [YYYY-MM-DD]
---

## Goal
[What Orlando wants from this project, in his words where you have them.]

## Constraints
[Rules he set for this project.]

## Current State
[What exists now. What was accomplished in the most recent session.]

## Key Decisions
[Important choices made and their reasoning. Accumulate these across sessions. Don&#x27;t discard prior decisions unless they&#x27;ve been reversed.]

## Next Steps
[What to pick up next time, in priority order.]

## Open Questions
[Anything unresolved or needing input.]
```

## Rules

- If `context.md` already exists, read it first and merge. Don&#x27;t overwrite prior decisions or context that&#x27;s still relevant. Update the `last-updated` date.
- If this is the first time, create the file from scratch based on the conversation.
- Current State describes the project now. Rewrite it each time; don&#x27;t stack dated updates.
- Before writing that something is done or not done, check the files. Don&#x27;t rely on memory of the conversation.
- Be concise. This file is for an LLM to read at the start of a session, not a full history.
- Only include information that would actually help a future session. Skip ephemeral details.
- Do not include information that can be derived from the code, git history, or existing project files.
</code></pre>

</figure>

</section>

<nav class="page-foot">

Previous: <a href="/ai-setup/skills/process-feedback/">Process feedback</a>.
Next: <a href="/ai-setup/skills/tune-setup/">Tune setup</a>.

<a href="/ai-setup/skills/">Back to Skills</a>

<a href="https://www.linkedin.com/in/oneillo/" target="_blank" rel="noopener">Borrowed something? Tell me how it went</a>

Text: <a href="https://creativecommons.org/licenses/by/4.0/" target="_blank" rel="license noopener">CC BY 4.0</a>. Scripts: <a href="https://opensource.org/license/mit" target="_blank" rel="license noopener">MIT</a>.

</nav>
