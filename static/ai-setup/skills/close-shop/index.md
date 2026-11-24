# Close shop

- Kind: Claude Code skill
- Address: https://oneillo.com/ai-setup/skills/close-shop/
- Last updated: 2026-11-22

> AI-written and cleaned from one person's working setup. Placeholders in angle brackets are not real values: ask your user for theirs. The newest version is at https://oneillo.com/ai-setup/skills/close-shop/.

Ends a working session in three steps: saves project context, checks corrections are in memory, and commits the session's changes.

- **What it is:** Close shop is a Claude Code skill that ends a working session by saving project context, checking memory, and committing changes.
- **How it starts:** You type `/close-shop`. Claude doesn't start it by itself.
- **Where the file goes:** `~/.claude/skills/close-shop/SKILL.md`
- **Needs:** git
- **To make it yours:** Put your own values where you see `<vault-history-folder>`.
  Taken out for privacy: 1 path.

**Works with:**
- **Calls:** [Process feedback](https://oneillo.com/ai-setup/skills/process-feedback/) and [Tune setup](https://oneillo.com/ai-setup/skills/tune-setup/).
- **Called by:** [Workspace rules](https://oneillo.com/ai-setup/rules/workspace/).

## The skill file (`SKILL.md`)

The skill's instructions for the end of a working session, covering saving project context, saving corrections to memory, and committing the session's changes to local git repos.

```
---
name: close-shop
description: End-of-session routine that saves project context, checks corrections are saved to memory, and commits this session's changes
disable-model-invocation: true
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, AskUserQuestion
---

# Close Shop

Run the end-of-session routine in three steps, in order.

## Step 1: Save session

Save for each project the session changed: a folder under `~/ai/projects/`, a tool under `~/ai/tools/`, or any folder that already has a `context.md`. Skip if no project files were read, edited, or created, or the session was just conversation or brainstorming. If skipping, say why in one line and move on.

Otherwise, read `~/.claude/skills/save-session/SKILL.md` and follow it.

Then promote durable knowledge. If the project produced anything that will stay true after the project ends (facts about Orlando, reference material, finished notes), move it into the knowledge base at `~/ai/knowledge-base/` (copy only when the project still needs the file, and say so), following its `AGENTS.md`, and run `uv run ~/ai/tools/scripts/kb.py check` on what you added. Then note in `context.md` what moved and where it lives now. Then connect it: follow `~/.claude/skills/connect/SKILL.md` for the notes you added or changed, so they link to the concepts they discuss and new recurring ideas get concept notes. Say in one line what you moved and connected, or that nothing qualified.

## Step 2: Check corrections are in memory

Scan the session for moments Orlando corrected the approach, had to repeat himself, or confirmed a non-obvious approach worked. For each lesson that would change how an agent works in a future session, make sure auto memory has it as a `feedback` memory (in the session's memory directory, following the auto memory format), and save it if not. Skip one-off slips and anything already in a CLAUDE.md, agent file or memory. Say in one line what you saved, or that nothing qualified. `/process-feedback` reviews them at each `/tune-setup`.

## Step 3: Commit this session's changes

`~/ai/`, `~/.claude/`, and the knowledge base (`~/ai/knowledge-base/`, its history kept in `<vault-history-folder>`) are local git repos with no remote. Orlando never runs git; you do it.

For each of the three repos:

1. Run `git -C <repo> status --short`.
2. Stage only the files this session created, edited, or deleted, including the `context.md` and memory changes from Steps 1 and 2. Stage by explicit path (`git -C <repo> add -- <paths>`). Never `git add -A` or `git add .` at the repo root.
3. Commit only those paths (`git -C <repo> commit -m "<subject>" -- <paths>`), with a subject of `<agent>: <what changed>` (agent is `writer`, `trainer`, `financial-advisor`, or `main` for a plain session), under 72 characters. Add a short body with bullets for the main changes and the project folder, if any.
4. If nothing from this session changed in a repo, skip it.

If the session changed files in a folder that has its own repo (listed in `~/ai/.gitignore`), commit there by that repo's own rules, never with `git add -f` from `~/ai`, and name that repo in the closing line. If that repo's own rules say to ask about pushing, ask Orlando.

Then handle leftovers: changes this session didn't make. These are usually Orlando's own edits or another session's, and left alone they sit uncommitted for days. If any remain in any of the three, list them and ask Orlando once whether another session is still working on them. If not, add and commit them by explicit path (`-- <paths>`, as above) with the subject `unattributed: <short summary>`. If one is, leave those files alone.

Never push (the question above is the one exception), amend, rebase, reset, or otherwise rewrite history. Never commit secrets; if a file looks like one, stop and ask.

Finish by telling Orlando, in one line per repo, what was committed (or that nothing was), plus anything left uncommitted and why.
```

Next: [Process feedback](https://oneillo.com/ai-setup/skills/process-feedback/).

[Back to Skills](https://oneillo.com/ai-setup/skills/)

[Borrowed something? Tell me how it went](https://www.linkedin.com/in/oneillo/)

Text: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Scripts: [MIT](https://opensource.org/license/mit).
