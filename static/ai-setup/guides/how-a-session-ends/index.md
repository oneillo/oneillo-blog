# How a session ends

- Kind: Claude Code walk-through
- Address: https://oneillo.com/ai-setup/guides/how-a-session-ends/
- Last updated: 2026-11-27

> AI-written and cleaned from one person's working setup. Placeholders in angle brackets are not real values: ask your user for theirs. The newest version is at https://oneillo.com/ai-setup/guides/how-a-session-ends/.

Shows how a session saves project state, turns corrections into lasting lessons, links new notes, and commits, so the next session starts clean.

When a working session ends, nothing learned should be lost. In this setup, Orlando types `/close-shop`, and the Close shop skill runs the whole routine in three steps. Each step leans on habits set in the Workspace rules, the file every session in the workspace reads first.

## Step 1: Save where the project stands

The Close shop skill first looks at each project the session changed. For each one, it follows the Save session skill. That skill reviews the conversation and writes or updates a `context.md` file at the project's root. The file holds the goal, the constraints, the current state, key decisions, next steps, and open questions.

The Save session skill keeps the file short. It merges with what is already there, keeps earlier decisions, and rewrites the current state each time instead of stacking dated updates. It also checks the files, not memory, before saying something is done. If the session was only conversation, the step is skipped, and the skill says why.

The Workspace rules set this habit. They say every project folder gets a `context.md`, and that decisions go into it right away so they survive a long session. `/save-session` then does the full update at the end.

Next, the Close shop skill moves anything that will stay true after the project ends into the knowledge base. Then it follows the Connect skill. That skill links the new or changed notes to the concept notes they discuss. It also creates a concept note when an idea now shows up across several sources, and it adds links between areas.

## Step 2: Turn corrections into lessons

The Close shop skill scans the session for moments Orlando corrected the approach or repeated himself. Each lesson that would change how an agent works goes into auto memory as a feedback memory.

Later, the Process feedback skill reviews those memories. It suggests one action for each: keep it, promote it to a rules file, agent, or skill, merge it, or delete it. It waits for Orlando's input before changing anything.

## Step 3: Commit

Last, the Close shop skill commits the session's changes in the workspace, the Claude Code config folder, and the knowledge base. It adds files by exact path and never pushes. The Workspace rules require this, so no finished work is left uncommitted.

## What you get

A project folder with a current `context.md`, new knowledge filed and linked, corrections kept as lessons, and a clean commit history. The next session reads the context file first and knows where to pick up.

## The pieces it joins

- [Save session](https://oneillo.com/ai-setup/skills/save-session/)
- [Close shop](https://oneillo.com/ai-setup/skills/close-shop/)
- [Process feedback](https://oneillo.com/ai-setup/skills/process-feedback/)
- [Connect](https://oneillo.com/ai-setup/skills/connect/)
- [Workspace rules](https://oneillo.com/ai-setup/rules/workspace/)

[Back to AI Setup](https://oneillo.com/ai-setup/)

[Borrowed something? Tell me how it went](https://www.linkedin.com/in/oneillo/)

Text: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Scripts: [MIT](https://opensource.org/license/mit).
