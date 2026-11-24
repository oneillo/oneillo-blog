# How a session ends

- Kind: Claude Code walk-through
- Address: https://oneillo.com/ai-setup/guides/how-a-session-ends/
- Last updated: 2026-11-24

> AI-written and cleaned from one person's working setup. Placeholders in angle brackets are not real values: ask your user for theirs. The newest version is at https://oneillo.com/ai-setup/guides/how-a-session-ends/.

Shows how a session ends: it saves project state, files lasting knowledge, keeps corrections, and commits, so the next session starts clean.

A working session ends with a short routine, so nothing learned is lost. Orlando starts it with `/close-shop`. The Close shop skill runs three steps in order, and it calls on other skills along the way.

## Where the habits come from

The Workspace rules, which every session in the workspace loads, set the habits behind all of this. They say every project folder has a `context.md` file, and that decisions go into it right away so they survive a long session. They also say the full update happens at the end, and that durable knowledge moves into the knowledge base when the session closes.

## Step 1: Save where the project stands

If the session changed a project, the Close shop skill hands over to the Save session skill. It finds the project folder, then creates or updates the `context.md` file there. If the file exists, it reads it first and merges, so earlier decisions are kept. The file holds the goal, constraints, current state, key decisions, next steps, and open questions. The current state is rewritten each time, not stacked up with dated notes. The skill checks the files before it writes that something is done. If the session was only conversation, Close shop says so in one line and moves on.

Next, anything that will stay true after the project ends moves into the knowledge base. That includes facts, reference material, and finished notes. Close shop notes the move in `context.md`. Then the Connect skill runs on the notes that were added or changed. It links them to the ideas they discuss, builds concept notes for ideas that now recur across sources, and adds links between areas. It finishes with checks on the knowledge base.

## Step 2: Keep the corrections

Close shop scans the session for moments when Orlando corrected the approach or had to repeat himself. Each lesson that would change how an agent works next time is saved as a `feedback` memory, unless it is already written down somewhere. Later, the Process feedback skill reviews those memories during a regular tune-up. It decides whether each one stays, moves into a rules file, an agent, or a skill, merges, or goes. Orlando approves before anything changes.

## Step 3: Commit

Close shop commits this session's changes in each of the three local repos, staging only the files it touched. It never pushes. It then lists any leftover changes it didn't make and asks Orlando before committing them.

## What you get

A project folder that tells the next session where to pick up, with knowledge filed, lessons saved, and the history committed.

## The pieces it joins

- [Save session](https://oneillo.com/ai-setup/skills/save-session/)
- [Close shop](https://oneillo.com/ai-setup/skills/close-shop/)
- [Process feedback](https://oneillo.com/ai-setup/skills/process-feedback/)
- [Connect](https://oneillo.com/ai-setup/skills/connect/)
- [Workspace rules](https://oneillo.com/ai-setup/rules/workspace/)

[Back to AI Setup](https://oneillo.com/ai-setup/)

[Borrowed something? Tell me how it went](https://www.linkedin.com/in/oneillo/)

Text: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Scripts: [MIT](https://opensource.org/license/mit).
