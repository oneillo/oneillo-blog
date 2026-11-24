# Rules

- Kind: Claude Code rules files
- Address: https://oneillo.com/ai-setup/rules/
- Last updated: 2026-11-22

> AI-written and cleaned from one person's working setup. Placeholders in angle brackets are not real values: ask your user for theirs. The newest version is at https://oneillo.com/ai-setup/rules/.

The rules files in Orlando's AI setup, the instructions every session starts from.

Rules come in layers. The global rules apply to every session, anywhere on the Mac. The workspace rules apply in the agent workspace, the folder where agents do their work. The knowledge base has its own rules file, for agents working in the notes.

- [Global rules](https://oneillo.com/ai-setup/rules/global/): Sets global rules for every session: direct style, keeping the Mac awake, file defaults, cleanup, Python with uv, and formatting.
- [Workspace rules](https://oneillo.com/ai-setup/rules/workspace/): Sets the shared rules for every session in a workspace: how to work together, where files live, how projects start, and how history is kept.
- [Knowledge base rules](https://oneillo.com/ai-setup/rules/knowledge-base/): Sets the rules agents follow to find, write, link, and check notes in a knowledge base kept as a vault of Markdown notes.

## Where each file lives

| File | Where it lives | When a session reads it |
|---|---|---|
| [Global rules](https://oneillo.com/ai-setup/rules/global/) | `~/.claude/CLAUDE.md` | Every Claude Code session reads it at the start. |
| [Workspace rules](https://oneillo.com/ai-setup/rules/workspace/) | `<your-workspace>/CLAUDE.md` | Every Claude Code session started in the workspace, or in a folder inside it, reads it at the start. |
| [Knowledge base rules](https://oneillo.com/ai-setup/rules/knowledge-base/) | In the knowledge base's own folder | Sessions read it before they work in the knowledge base. |

## Project rules and context files

Each project adds its own rules file, and a context file that holds the project's goal, constraints, and decisions. A session reads them before it starts, so it can pick up where the last one stopped. They hold each project's private detail, so their text is not shared here.

## Memory

Claude Code keeps a memory for the workspace and one for each custom agent: lessons learned while working, and where things are. Memory is private by nature. It is never read for this section, and nothing here comes from it.

## Pages

- [Global rules](https://oneillo.com/ai-setup/rules/global/index.md): Sets global rules for every session: direct style, keeping the Mac awake, file defaults, cleanup, Python with uv, and formatting.
- [Workspace rules](https://oneillo.com/ai-setup/rules/workspace/index.md): Sets the shared rules for every session in a workspace: how to work together, where files live, how projects start, and how history is kept.
- [Knowledge base rules](https://oneillo.com/ai-setup/rules/knowledge-base/index.md): Sets the rules agents follow to find, write, link, and check notes in a knowledge base kept as a vault of Markdown notes.
