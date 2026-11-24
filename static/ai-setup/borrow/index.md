# How to borrow

- Kind: Claude Code borrowing guide
- Address: https://oneillo.com/ai-setup/borrow/
- Last updated: 2026-11-24

> AI-written and cleaned from one person's working setup. Placeholders in angle brackets are not real values: ask your user for theirs. The newest version is at https://oneillo.com/ai-setup/borrow/.

How to take a rule, skill, agent, or hook from Orlando's AI setup and make it work in your own Claude Code.

Every rule, skill, agent, and hook here is a plain text file. To borrow one, copy it into the right place in your own Claude Code setup, then make it yours.

## Where each file goes

- **Global rules:** `~/.claude/CLAUDE.md`
- **Workspace rules:** `<your-workspace>/CLAUDE.md`
- **A skill:** `~/.claude/skills/<name>/SKILL.md`, its other files beside it
- **A custom agent:** `~/.claude/agents/<name>.md`
- **A hook:** `~/.claude/hooks/<file>`, set up in `~/.claude/settings.json`

## Try one skill safely

1. Read the whole page first, scripts too. Each one is one person's working setup, not a supported tool.
2. Make a throwaway folder and put the skill in it, at `.claude/skills/<name>/SKILL.md`. There it works only for sessions started in that folder.
3. Put your own values where you see placeholders like `<project-folder>`.
4. Start a session in that folder, type `/<name>`, and watch what it does.
5. Once it does what you want, move it to `~/.claude/skills/<name>/`.

## Hand it to your agent

Give your agent the page's address and paste this:

```
Read this page and adapt it to my setup. Ask me for each placeholder.
```

[Back to AI Setup](https://oneillo.com/ai-setup/)

[Borrowed something? Tell me how it went](https://www.linkedin.com/in/oneillo/)

Text: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Scripts: [MIT](https://opensource.org/license/mit).
