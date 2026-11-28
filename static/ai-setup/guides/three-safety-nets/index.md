# Three safety nets

- Kind: Claude Code walk-through
- Address: https://oneillo.com/ai-setup/guides/three-safety-nets/
- Last updated: 2026-11-28

> AI-written and cleaned from one person's working setup. Placeholders in angle brackets are not real values: ask your user for theirs. The newest version is at https://oneillo.com/ai-setup/guides/three-safety-nets/.

Shows the three ways to undo an agent's mistake: the Trash, local version history, and backups, from the smallest fix to the largest.

Agents make mistakes. This setup has three layers that let you undo them. They run from the smallest fix to the largest, and each one covers a different kind of problem.

## 1. The Trash

The smallest safety net is the Trash. The Workspace rules tell agents to delete with the `trash` command, not `rm`. A file that is deleted this way is moved to the Trash, so a mistake can be recovered by taking the file back out.

Before an agent deletes something it didn't create, it checks that nothing else refers to it. There is one exception. Inside the scratch folder `tmp/`, plain `rm` is fine. Anything there may be deleted at any time, and the real originals live in project folders.

Use the Trash when a single file or folder was removed by mistake.

## 2. Local version history

The second net is version history. The workspace, the Claude Code configuration (agents, skills, and memory), and the knowledge base each keep their own local git history. Agents handle git, so Orlando never has to.

Sessions commit their finished work, and the `/close-shop` skill commits at the end of a session. Before a bulk change, such as moving, renaming, or deleting many files, an agent first commits a checkpoint. That way the whole change can be undone in one step.

To undo something, the agent first confirms with Orlando. Then it restores the specific files that need it. It does not reset the whole repository. These histories are never pushed anywhere.

Use this net when an edit went wrong, or when a larger change needs to be rolled back.

## 3. Backups

The largest net is the backup layer. The workspace page says that backups are part of the design, so agents can work safely and changes can be undone. The pieces here say nothing more about how backups work or how to restore from them, so this walk-through doesn't either. Treat it as the last resort, for when the first two nets can't help.

## How they fit together

Start with the smallest net that fits the problem. Check the Trash for a deleted file first, then the version history for a bad edit. Backups come last.

## The pieces it joins

- [How it's organized](https://oneillo.com/ai-setup/workspace/)
- [Workspace rules](https://oneillo.com/ai-setup/rules/workspace/)

[Back to AI Setup](https://oneillo.com/ai-setup/)

[Borrowed something? Tell me how it went](https://www.linkedin.com/in/oneillo/)

Text: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Scripts: [MIT](https://opensource.org/license/mit).
