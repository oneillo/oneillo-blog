# How a session ends

- Kind: Claude Code walk-through
- Address: https://oneillo.com/ai-setup/guides/how-a-session-ends/
- Last updated: 2026-11-28

> AI-written and cleaned from one person's working setup. Placeholders in angle brackets are not real values: ask your user for theirs. The newest version is at https://oneillo.com/ai-setup/guides/how-a-session-ends/.

Shows how a session saves project context, checks corrections are in memory, links new notes, and commits before it ends.

A working session ends with one command: `/close-shop`. The Close shop skill runs three steps in order, so nothing learned is lost and the next session starts clean.

## Step 1: Save where the project stands

For each project the session changed, the Close shop skill follows the Save session skill. That skill writes a `context.md` file at the root of the project folder. The file holds the goal, constraints, current state, key decisions, next steps, and open questions.

The skill keeps the file short, because a future session reads it at the start. It merges with any existing file, keeps earlier decisions, and rewrites the current state instead of stacking dated updates. It also checks the files before claiming something is done.

The Workspace rules set this habit. They say every project folder gets a `context.md` when it is created. They also say to read it first when a project is resumed.

Next, the Close shop skill moves anything that will stay true after the project ends into the knowledge base. It records the move in `context.md`. Then it follows the Connect skill for the notes it added or changed. That skill links each note to the concept notes it discusses. It also creates a concept note when an idea now recurs across sources.

## Step 2: Check corrections are in memory

The skill scans the session for moments when Orlando corrected the approach, repeated himself, or confirmed that a non-obvious approach worked. Each lesson that would change how an agent works goes into memory as a `feedback` memory. This follows the Workspace rules, which send corrections to memory instead of a separate log.

Later, the Process feedback skill (`/process-feedback`) reviews those memories. For each one, it suggests an action: keep it, merge it, delete it, or promote it to a rules file, an agent, a skill, or a hook. Nothing changes until Orlando agrees.

## Step 3: Commit

The workspace, the Claude Code config, and the knowledge base each keep a local git history. The Close shop skill commits this session's changes by explicit path, including the `context.md` and memory changes. It never pushes. Changes from other sessions are listed, and Orlando is asked about them once.

## What you get

A project folder with a `context.md` that tells the next session where to pick up, lessons saved in memory, and every change committed.

## The pieces it joins

- [Save session](https://oneillo.com/ai-setup/skills/save-session/)
- [Close shop](https://oneillo.com/ai-setup/skills/close-shop/)
- [Process feedback](https://oneillo.com/ai-setup/skills/process-feedback/)
- [Connect](https://oneillo.com/ai-setup/skills/connect/)
- [Workspace rules](https://oneillo.com/ai-setup/rules/workspace/)

[Back to AI Setup](https://oneillo.com/ai-setup/)

[Borrowed something? Tell me how it went](https://www.linkedin.com/in/oneillo/)

Text: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Scripts: [MIT](https://opensource.org/license/mit).
