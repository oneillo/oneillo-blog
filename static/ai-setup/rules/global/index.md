# Global rules

- Kind: Claude Code rules file
- Address: https://oneillo.com/ai-setup/rules/global/
- Last updated: 2026-11-27
- Also changed: 2026-11-22

> AI-written and cleaned from one person's working setup. Placeholders in angle brackets are not real values: ask your user for theirs. The newest version is at https://oneillo.com/ai-setup/rules/global/.

Sets the style, file habits, cleanup duties, and Python rules that every Claude Code session and agent follows.

- **What it is:** The global steering file is a Claude Code rules file (global) that sets how every session and agent writes, keeps the Mac awake, handles files, cleans up, and runs Python.
- **How it starts:** Every Claude Code session reads it at the start.
- **Where the file goes:** `~/.claude/CLAUDE.md`
- **Needs:** Rex, uv, and Chrome

**Works with:**
- **Also mentions:** [Rex](https://oneillo.com/ai-setup/skills/rex/).

## The rules file (`CLAUDE.md`)

The global rules file that every Claude Code session reads. It sets the writing style, keeps the Mac awake during long jobs, and covers file habits, cleanup, the browser, and Python.

````
# Global Steering

For all Claude Code sessions and agents.

## Style

- Be direct: lead with the answer or action. No preamble, filler, trailing summaries, or restating a diff I can read.
- No compliment sandwiches. If something is wrong, say so clearly.
- Plain language, like a smart colleague, not a textbook. Short, direct sentences.
- Headers and lists for multi-part answers; don't over-structure short ones.
- No emojis unless I ask.
- No em dashes, in chat or files (they read as AI-written): use commas, colons, periods or parentheses.

## Keeping the Mac Awake

- When I step away, work with you over Remote Control, or a job runs unattended, keep the Mac awake without being asked: wrap the long job in `caffeinate -ims <command>` (lasts exactly as long as it runs), or start one background `caffeinate -ims -t <seconds>` sized to the work, unless one is already running. The display may sleep (Remote Control doesn't need it). Stop only what you started.
- While the front desk (`~/ai/tools/scripts/front-desk.sh`, in its own Rex tab) runs with `--until`, it keeps the Mac awake: start nothing more.
- When I step away and the session isn't on Remote Control, suggest `/remote-control`. While it is, send a push notification when work is ready for my feedback or blocked on me, and each time a reply ends with a question for me. Never for routine progress.

## Files

- Unless I ask otherwise: Markdown, with descriptive lowercase-hyphenated names (`my-topic-notes.md`).
- Read and write only within `~/ai/` and `~/.claude/` unless I direct otherwise. Ask before creating files elsewhere.

## Cleaning Up

- Clean up after yourself, and brief every agent you start to do the same. Whatever a task creates that isn't the deliverable (temp folders, test data, caches, builds, downloads, worktrees, background processes) is removed when the task ends. Tests remove what they create as they finish.
- Leftovers that grow with use are a leak, like a memory leak, including in the system temp folder (`$TMPDIR`), not just the project folder.
- Before long, repeated or parallel runs: check what each run leaves behind and where, check free space (`df -h`), and put a cleanup or cap in place. Recheck free space at milestones.

## Browser

- Claude in Chrome is off by default. When a task needs the browser, start that session with `claude --chrome`.

## Python

- Run scripts with `uv run`. No virtual environments, no pip.
- Always include inline script metadata (omit `dependencies` if stdlib only):
  ```
  # /// script
  # requires-python = ">=3.12"
  # dependencies = ["requests"]
  # ///
  ```
````

Next: [Workspace rules](https://oneillo.com/ai-setup/rules/workspace/).

[Back to Rules](https://oneillo.com/ai-setup/rules/)

[Borrowed something? Tell me how it went](https://www.linkedin.com/in/oneillo/)

Text: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Scripts: [MIT](https://opensource.org/license/mit).
