# Three safety nets

- Kind: Claude Code walk-through
- Address: https://oneillo.com/ai-setup/guides/three-safety-nets/
- Last updated: 2026-11-24

> AI-written and cleaned from one person's working setup. Placeholders in angle brackets are not real values: ask your user for theirs. The newest version is at https://oneillo.com/ai-setup/guides/three-safety-nets/.

Shows the three ways Orlando's setup undoes an agent's mistake: the Trash, local version history, and the cloud copy, and when each one helps.

Agents make mistakes. Orlando's setup gives him three ways to undo one, from the smallest to the largest. Each covers a bigger kind of loss than the one before.

## 1. The Trash

The Workspace rules tell agents to delete with the `trash` command, not `rm`. A deleted file moves to the Trash instead of vanishing. If an agent removes the wrong file, Orlando can bring it back from there.

The rules add two limits. Inside the scratch folder, `rm` is fine, because nothing vital lives there. And before an agent deletes something it didn't create, it checks that nothing else refers to it.

## 2. Local version history

The workspace, the Claude Code config folder, and the knowledge base vault are each a local git repo. They have no remote, and agents never push them. Agents do all the git work, so Orlando never runs it.

An agent commits at the end of each session, when the Close shop skill runs with `/close-shop`. Before a bulk change, such as moving, renaming, or deleting many files, it commits a checkpoint first. That way the change can be undone in one step.

Every committed change is kept. To undo something, the agent confirms with Orlando, then restores the specific files. It does not reset the whole repo.

## 3. The cloud copy

The third net is the largest. A backup copies the workspace and the Claude Code config folder to cloud storage every 30 minutes, on a timer. Scratch files are not backed up.

This one helps when a file is gone completely. In Orlando's words, he can generally recover it from the cloud storage's recycle bin online.

The same copy also covers a lost or replaced computer. To restore, you follow these steps:

1. Sign in to the cloud storage and let the backup folder download fully.
2. Install the tools the setup needs, such as Claude Code and uv.
3. Copy the backed-up folders back, without deleting anything.
4. Check that every git repo still has its history.
5. Do a dry run of the backup, and stop if the list of deletions is long.
6. Turn the backup timer back on last.

## Which one to reach for

Start small. A wrongly deleted file comes back from the Trash. A bad edit is undone by restoring files from git. A file that is truly gone, or a whole machine, comes back from the cloud copy.

## The pieces it joins

- [How it's organized](https://oneillo.com/ai-setup/workspace/)
- [Workspace rules](https://oneillo.com/ai-setup/rules/workspace/)

[Back to AI Setup](https://oneillo.com/ai-setup/)

[Borrowed something? Tell me how it went](https://www.linkedin.com/in/oneillo/)

Text: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Scripts: [MIT](https://opensource.org/license/mit).
