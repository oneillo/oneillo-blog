# Three safety nets

- Kind: Claude Code walk-through
- Address: https://oneillo.com/ai-setup/guides/three-safety-nets/
- Last updated: 2026-11-27

> AI-written and cleaned from one person's working setup. Placeholders in angle brackets are not real values: ask your user for theirs. The newest version is at https://oneillo.com/ai-setup/guides/three-safety-nets/.

Shows three ways to undo an agent's mistake, from the Trash to local version history to a cloud copy, and when each one helps.

Agents make mistakes. Orlando's setup assumes they will, so it has three safety nets. Each one catches a bigger problem than the one before it. The Workspace rules and the page on how the workspace is organized describe them.

## 1. The Trash

The smallest net is the Trash. The Workspace rules tell every agent to delete with `trash`, not `rm`. A deleted file is moved to the Trash instead of being erased, so it can be brought back. The one exception is the scratch folder, where `rm` is fine because nothing vital lives there.

Use this net when a single file went missing. The rules also ask an agent to check that nothing refers to a file it didn't create before deleting it.

## 2. Local version history

The next net is git. The workspace, the Claude Code config folder, and the knowledge base vault each keep a local history. None of them has a remote, so nothing is pushed. Agents handle all of the git work. A commit is made at the end of each session, and a checkpoint is made before any bulk change, such as moving or renaming many files. That way a large change can be undone in one step.

Use this net when an agent changed a file, or many files, in a way you don't want. To undo something, the agent first confirms with Orlando. Then it restores the specific files. It never resets the whole repo.

## 3. The cloud copy

The largest net is a copy of the work kept off the Mac. It covers the workspace folder, the Claude Code config folder, and the blog's working folder. A timer refreshes it every 30 minutes. The copy mirrors the Mac, deletions included. In Orlando's words, if an agent ever removes a file completely, he can generally recover it from the cloud storage's recycle bin online.

Safeguards sit around the copy. It refuses to run if the cloud folder is missing. It warns when a run would delete an unusually large number of files. It also warns when a git history disappears.

Use this net when the first two have failed, or when the whole Mac is gone. To rebuild on a new Mac:

1. Sign in to the cloud storage and wait for the backup to download fully.
2. Install the tools.
3. Copy the folders back from the cloud copy, without deleting anything.
4. Check that every git repo answers.
5. Do a dry run of what the backup would delete, and stop if the list is long.
6. Load the backup timer last, because it starts mirroring the moment it is loaded.

## Why three

Each net covers a gap in the others. The Trash can't undo a bad edit. Git can't help if a whole folder is lost. The cloud copy may be up to half an hour behind. Together, any change an agent makes can be seen and undone.

## The pieces it joins

- [How it's organized](https://oneillo.com/ai-setup/workspace/)
- [Workspace rules](https://oneillo.com/ai-setup/rules/workspace/)

[Back to AI Setup](https://oneillo.com/ai-setup/)

[Borrowed something? Tell me how it went](https://www.linkedin.com/in/oneillo/)

Text: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Scripts: [MIT](https://opensource.org/license/mit).
