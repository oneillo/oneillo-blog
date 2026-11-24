# How it's organized

- Kind: Claude Code workspace
- Address: https://oneillo.com/ai-setup/workspace/
- Last updated: 2026-11-22

> AI-written and cleaned from one person's working setup. Placeholders in angle brackets are not real values: ask your user for theirs. The newest version is at https://oneillo.com/ai-setup/workspace/.

How Orlando's AI workspace is organized: its folders, the knowledge base, version history, backups, and Python scripts.

This page explains how Orlando's setup is organized: one workspace for agents, a knowledge base, local git history, an automatic cloud backup, and Python scripts that carry their own needs.

## The workspace

One folder holds everything. It has the knowledge base, projects sorted by area in dated folders that each have a context file, finished tools, and a scratch folder. The scratch folder is swept daily and never holds anything vital.

## The knowledge base

The knowledge base is a vault of notes. It is split into areas, and each area has a hub note. Concept notes link back to their sources. A scratch folder holds notes for Orlando to review. Agents start from the vault's rules file and its index. When a project closes, the lasting facts move into the knowledge base.

## Version history with git

The workspace, the Claude Code config folder, and the vault are each a local git repo with no remote. Agents do all the git work. There is a commit at the end of each session and a checkpoint before a bulk change. Undoing means restoring files. Why: any change an agent makes can be seen and undone.

## Backups

"If an agent ever accidentally removes a file completely, I can generally recover it from the cloud storage's recycle bin online."

The backup is one of three safety nets:

1. Deletes go to the Trash, so a mistake can be recovered from there.
2. Local version history keeps every committed change.
3. The cloud copy mirrors the workspace and the Claude Code config folder.

His backup goes to OneDrive (cloud storage). It copies `~/ai/` and `~/.claude/` every 30 minutes, on a timer. A private GitHub repo or similar is another way to do it, with one catch: it has no recycle bin for a file deleted between commits, so it needs frequent automatic commits.

To restore, follow these steps:

1. Sign in to the cloud storage on the new computer and let the backup folder download fully.
2. Install the tools the setup needs, such as Claude Code and uv.
3. Copy the backed-up folders back to `~/ai/` and `~/.claude/`, without deleting anything.
4. Check that every git repo answers and still has its history.
5. Do a dry run of the backup to see what it would delete, and stop if the list is long.
6. Turn the backup timer back on last, so it never mirrors a half-restored computer over the backup.

## Python with uv

Every script runs with `uv run` and declares its Python version and packages at its top. There are no virtual environments and no `pip`. Why: a script carries what it needs, nothing is installed system-wide, there is nothing to set up or clean up, and an agent can write and run a one-off script safely.

```
# /// script
# requires-python = ">=3.12"
# dependencies = ["requests"]
# ///
```

**Works with:** [Brega](https://oneillo.com/ai-setup/apps/#brega), [Close shop](https://oneillo.com/ai-setup/skills/close-shop/), [Corillo](https://oneillo.com/ai-setup/apps/#corillo), [Financial advisor](https://oneillo.com/ai-setup/agents/financial-advisor/), [Heartbeat](https://oneillo.com/ai-setup/skills/heartbeat/), [Process feedback](https://oneillo.com/ai-setup/skills/process-feedback/), [Rex](https://oneillo.com/ai-setup/skills/rex/), [Save session](https://oneillo.com/ai-setup/skills/save-session/), [Trainer](https://oneillo.com/ai-setup/agents/trainer/), [Tune setup](https://oneillo.com/ai-setup/skills/tune-setup/), and [Writer](https://oneillo.com/ai-setup/agents/writer/).

The same rules appear as text on the [Global rules](https://oneillo.com/ai-setup/rules/global/) and [Workspace rules](https://oneillo.com/ai-setup/rules/workspace/) pages.
