# Close shop

- Kind: Claude Code skill
- Address: https://oneillo.com/ai-setup/skills/close-shop/
- Last updated: 2026-11-27
- Also changed: 2026-11-22

> AI-written and cleaned from one person's working setup. Placeholders in angle brackets are not real values: ask your user for theirs. The newest version is at https://oneillo.com/ai-setup/skills/close-shop/.

Ends a working session in order: saves project context, checks corrections are in memory, and commits the session's changes.

- **What it is:** Close shop is a Claude Code skill that ends a working session in three ordered steps.
- **How it starts:** You type `/close-shop`. Claude doesn't start it by itself.
- **Where the file goes:** `~/.claude/skills/close-shop/SKILL.md`
- **Needs:** git and uv
- **Taken out for privacy:** 1 list of permissions, 2 paths, and 1 detail of how history is kept.

**Works with:**
- **Called by:** [Workspace rules](https://oneillo.com/ai-setup/rules/workspace/).

## The skill file (`SKILL.md`)

The main file of the skill. It sets out the three steps run at the end of a session: save project context, check that corrections are in memory, and commit the session's changes.

```
---
name: close-shop
description: Runs the end-of-session routine: saves project context, checks corrections are in memory, and commits this session's changes. Use when Orlando types /close-shop or asks to close shop.
disable-model-invocation: true
[A list of things the skill may do is left out here.]
---

# Close Shop

Three steps, in order.

## Step 1: Save session

For each project the session changed (a folder under `~/ai/projects/`, a tool under `~/ai/tools/`, or any folder with a `context.md`), read `~/.claude/skills/save-session/SKILL.md` and follow it. Skip if no project files were read, edited or created, or the session was only conversation or brainstorming; say why in one line.

Then promote durable knowledge: anything that will stay true after the project ends (facts about Orlando, reference material, finished notes).

1. Move it into `~/ai/knowledge-base/` following its `AGENTS.md` (copy only when the project still needs the file, and say so).
2. Run `uv run ~/ai/tools/scripts/kb.py check` on what you added.
3. Note in `context.md` what moved and where it lives now.
4. Follow `~/.claude/skills/connect/SKILL.md` for the notes added or changed.

Say in one line what you moved and connected, or that nothing qualified.

## Step 2: Check corrections are in memory

Scan the session for moments Orlando corrected the approach, repeated himself, or confirmed a non-obvious approach worked. Each lesson that would change how an agent works in a future session must be in auto memory as a `feedback` memory (session's memory directory, auto memory format); save it if not. Skip one-off slips and anything already in a CLAUDE.md, agent file or memory. Say in one line what you saved, or that nothing qualified.

## Step 3: Commit this session's changes

The workspace, the Claude Code config folder, and the knowledge base are local git repos. Orlando never runs git; you do.

For each of the three:

1. `git -C <repo> status --short`.
2. Stage only files this session created, edited or deleted, including Step 1 and 2 `context.md` and memory changes, by explicit path (`git -C <repo> add -- <paths>`). Never `git add -A` or `git add .` at the root.
3. `git -C <repo> commit -m "<subject>" -- <paths>`. Subject `<agent>: <what changed>` (`writer`, `trainer`, `financial-advisor`, or `main` for a plain session), under 72 characters. Short body: bullets for the main changes and the project folder, if any.
4. Skip a repo this session didn't change.

A folder with its own repo (listed in the workspace's ignore file): commit there by that repo's own rules, never `git add -f` from the workspace, and name it in the closing line. If its rules say to ask about pushing, ask Orlando.

Leftovers (changes this session didn't make, usually Orlando's or another session's; left alone they sit uncommitted for days): if any remain in the three repos, list them and ask Orlando once whether another session is still working on them. If not, commit them by explicit path with subject `unattributed: <short summary>`. If one is, leave those files alone.

Never push (the own-repo push question is the one exception), amend, rebase, reset or otherwise rewrite history. Never commit anything that looks like a secret; if a file looks like one, stop and ask.

Finish with one line per repo to Orlando: what was committed (or nothing), plus anything left uncommitted and why.
```

Next: [Process feedback](https://oneillo.com/ai-setup/skills/process-feedback/).

[Back to Skills](https://oneillo.com/ai-setup/skills/)

[Borrowed something? Tell me how it went](https://www.linkedin.com/in/oneillo/)

Text: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Scripts: [MIT](https://opensource.org/license/mit).
