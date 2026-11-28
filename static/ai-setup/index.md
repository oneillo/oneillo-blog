# AI Setup

- Kind: Claude Code setup
- Address: https://oneillo.com/ai-setup/

> AI-written and cleaned from one person's working setup. Placeholders in angle brackets are not real values: ask your user for theirs. The newest version is at https://oneillo.com/ai-setup/.

A tour of Orlando's personal AI setup: how it's organized, rules, skills, agents, apps, and settings.

## What this is

This section is for people, and their agents, who want to borrow from a working AI setup. Every text here can be copied and adapted.

It is one person's setup, Orlando's, built around Claude Code on a Mac. It is not a product.

It changes often. Every page says when its own content last changed.

The diagram as text:

- How Orlando's AI setup fits together.
- Orlando chats with a Claude Code session from the Mac or his phone.
- Orlando uses his apps: Corillo (review documents with an agent), Brega (a to-do list shared with agents), Pregón (publish a post), and Latido (a window onto the heartbeat).
- Corillo and Brega pass comments, edits, and tasks to a session and back.
- While Orlando is away, the Heartbeat (jobs on a timer) and the Relay (a roadmap, one fresh session per batch) start sessions.
- A session reads the rules at the start: global, workspace, and project.
- Settings and hooks check each step a session takes.
- A session can use 11 skills: close-shop, save-session, process-feedback, tune-setup, connect, publish-setup, podcast, relay, heartbeat, rex, and evals.
- A session can work as one of 3 custom agents: writer, trainer, and financial advisor.
- A session reads and writes the files on the Mac: the knowledge base (an Obsidian vault), projects (by area, in dated folders), tools (finished and reusable), and scratch (throwaway files).
- The files are kept under local version history and backed up to the cloud.

## Start here

1. [Global rules](https://oneillo.com/ai-setup/rules/global/): short and general, the rules every session starts from.
2. [Save session](https://oneillo.com/ai-setup/skills/save-session/) and [Close shop](https://oneillo.com/ai-setup/skills/close-shop/): the habit of a context file per project, so the next session picks up where the last one stopped.
3. [No em dash](https://oneillo.com/ai-setup/settings/#no-em-dash): a small hook with one clear job, stopping em dashes in anything an agent writes.

## By what you want to do

- **Pick up where you left off:** [Save session](https://oneillo.com/ai-setup/skills/save-session/), [Close shop](https://oneillo.com/ai-setup/skills/close-shop/), and [Workspace rules](https://oneillo.com/ai-setup/rules/workspace/).
- **Let work run while you're away:** [Relay](https://oneillo.com/ai-setup/skills/relay/), [Heartbeat](https://oneillo.com/ai-setup/skills/heartbeat/), and [the keep-awake rule](https://oneillo.com/ai-setup/rules/global/).
- **Undo an agent's mistake:** version history, backups, and the Trash, all on [How it's organized](https://oneillo.com/ai-setup/workspace/).
- **Keep the setup lean:** [Process feedback](https://oneillo.com/ai-setup/skills/process-feedback/) and [Tune setup](https://oneillo.com/ai-setup/skills/tune-setup/).
- **Write and publish:** [Writer](https://oneillo.com/ai-setup/agents/writer/).

## The pieces

- [How it's organized](https://oneillo.com/ai-setup/workspace/): the workspace's folders, the knowledge base's shape, version history, backups, and how Python scripts run.
- [Rules](https://oneillo.com/ai-setup/rules/): the rules every session starts from (3 rules files).
- [Skills](https://oneillo.com/ai-setup/skills/): written routines a session can run (11 skills).
- [Custom agents](https://oneillo.com/ai-setup/agents/): roles a session can take, each with its own prompt (3 agents).
- [Apps](https://oneillo.com/ai-setup/apps/): the apps around the sessions, described (6 apps).
- [Settings and hooks](https://oneillo.com/ai-setup/settings/): the switches underneath (2 hooks).

Orlando's weekly AI digest is public too, as the [Newsletter](https://oneillo.com/newsletter/).

## Walk-throughs

Each one follows a job across several pieces, and links to each.

- [How a session ends](https://oneillo.com/ai-setup/guides/how-a-session-ends/): Shows how a session saves project context, checks corrections are in memory, links new notes, and commits before it ends.
- [How work runs while I'm away](https://oneillo.com/ai-setup/guides/while-im-away/): Shows how the keep-awake rule, the Relay skill, the Heartbeat skill, and the Latido app keep work going and report back while Orlando is away.
- [Three safety nets](https://oneillo.com/ai-setup/guides/three-safety-nets/): Shows the three ways to undo an agent's mistake: the Trash, local version history, and backups, from the smallest fix to the largest.

## What this setup runs on

A Mac, Claude Code, Rex (the terminal app), Obsidian (the knowledge base), Hugo (the blog), uv (for Python scripts), git (for version history), and ffmpeg (for audio and video).

## How this section is made

1. Orlando starts a publish when he wants one.
2. The run gathers his rules, skills, and agents from his Mac, from a fixed list of places.
3. An AI model takes out anything private and writes the short descriptions.
4. A separate check holds back any page where something private is left: that page is shown as described only, or left out until the next publish.
5. The pages are built. At first Orlando looks at what changed and says yes before it goes live.

Nobody edits the AI-written text by hand. A note marked as Orlando's is his own words. A description may be wrong where the text under it is right: the text is the source.

## Borrowing from it

Every text here can be copied and made your own. [How to borrow](https://oneillo.com/ai-setup/borrow/) says where each file goes, how to try one safely, and what to ask your own agent.

## Follow changes

[What changed](https://oneillo.com/ai-setup/changes/) lists every publish, newest first, with the pieces each one added, changed, or removed.

The section's [feed](/ai-setup/index.xml) lists only its main pages, each when it first comes out. It doesn't list each rule, skill, or agent page, or say when a page changes.

## Pages

- [How it's organized](https://oneillo.com/ai-setup/workspace/index.md): How Orlando's AI workspace is organized: its folders, the knowledge base, version history, backups, and Python scripts.
- [Rules](https://oneillo.com/ai-setup/rules/index.md): The rules files in Orlando's AI setup, the instructions every session starts from.
- [Skills](https://oneillo.com/ai-setup/skills/index.md): The skills in Orlando's AI setup, the written routines a session can run.
- [Custom agents](https://oneillo.com/ai-setup/agents/index.md): The custom agents in Orlando's AI setup, the roles a session can take.
- [Apps](https://oneillo.com/ai-setup/apps/index.md): The apps around Orlando's AI sessions, each described in a few lines.
- [Settings and hooks](https://oneillo.com/ai-setup/settings/index.md): The settings and hooks in Orlando's AI setup, described by kind.
- [How to borrow](https://oneillo.com/ai-setup/borrow/index.md): How to take a rule, skill, agent, or hook from Orlando's AI setup and make it work in your own Claude Code.
- [What changed](https://oneillo.com/ai-setup/changes/index.md): Every publish of Orlando's AI setup, newest first, with the pieces each one added, changed, or removed.
