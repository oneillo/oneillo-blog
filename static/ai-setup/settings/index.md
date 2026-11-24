# Settings and hooks

- Kind: Claude Code settings and hooks
- Address: https://oneillo.com/ai-setup/settings/
- Last updated: 2026-11-23

> AI-written and cleaned from one person's working setup. Placeholders in angle brackets are not real values: ask your user for theirs. The newest version is at https://oneillo.com/ai-setup/settings/.

The settings and hooks in Orlando's AI setup, described by kind.

Sets up hooks, a plugin, a status line, and permissions so Claude Code sessions start and run the way the setup expects.

Two hooks run before tools are used and when a session starts. The frontend-design plugin is turned on.

## Permissions

There is a default mode setting left open, and no list of things sessions must refuse.

## Plugins and the status line

One plugin is turned on: frontend-design. The status line shows the model, the folder, and how much of the context window is used.

## Hooks

The settings set up 2 hooks: commands Claude Code runs at set points in a session.

### No em dash

- **Where the file goes:** `~/.claude/hooks/no-em-dash.sh`, set up in `~/.claude/settings.json`

Blocks file writes and edits that add em dashes, while leaving em dashes already in the text alone.

**Works with:**
- **Also mentions:** [Corillo](https://oneillo.com/ai-setup/apps/#corillo).

## The hook script (`no-em-dash.sh`)

A hook script that runs before a write or edit tool call and blocks it if the new text has more em dashes than the text it replaces. It tells Claude to rewrite with other punctuation.

```
#!/bin/bash
# PreToolUse hook: block a Write or Edit that adds em dashes.
# Compares the new text with the text it replaces, so em dashes already in a file
# (e.g. Orlando's own writing) never trigger it; only newly added ones do.
# Exit 2 blocks the tool call and shows stderr to Claude.
# It also covers Corillo's create, replace and edit tools, where Orlando reads
# what agents write. It names those tools: if Corillo renames them, it stops
# checking them silently (update the matcher in settings.json too).

input=$(cat)
tool=$(echo "$input" | jq -r '.tool_name // empty')
path=$(echo "$input" | jq -r '.tool_input.file_path // empty')

case "$path" in
  "$HOME"/.claude/skills/synced/*|"$HOME"/.claude/hooks/*) exit 0 ;;
esac

count() { jq -r "($1 // \"\") | [scan(\"—\")] | length" <<<"$input"; }

case "$tool" in
  Write)
    new=$(count .tool_input.content)
    old=0
    [ -f "$path" ] && old=$(grep -o '—' "$path" | wc -l | tr -d ' ')
    ;;
  Edit)
    new=$(count .tool_input.new_string)
    old=$(count .tool_input.old_string)
    ;;
  mcp__corillo__create_document|mcp__corillo__replace_document)
    path=$(echo "$input" | jq -r '.tool_input.path // empty')
    new=$(count .tool_input.text)
    old=0
    [ -f "$path" ] && old=$(grep -o '—' "$path" | wc -l | tr -d ' ')
    ;;
  mcp__corillo__edit_document)
    new=$(count '([.tool_input.edits[]?.replace // ""] | join(" "))')
    old=$(count '([.tool_input.edits[]?.find // ""] | join(" "))')
    ;;
  *) exit 0 ;;
esac

[ "${new:-0}" -gt "${old:-0}" ] || exit 0

echo "Blocked: this adds $((new - old)) em dash(es). Rewrite with commas, periods, colons, or parentheses, then retry. Em dashes already in the text you're replacing can stay." >&2
exit 2
```

## Connectors

[Corillo](https://oneillo.com/ai-setup/apps/#corillo) and [Brega](https://oneillo.com/ai-setup/apps/#brega) connect to Claude Code, so a session can use them. Each one has its section on the [Apps](https://oneillo.com/ai-setup/apps/) page.

[Back to AI Setup](https://oneillo.com/ai-setup/)

[Borrowed something? Tell me how it went](https://www.linkedin.com/in/oneillo/)

Text: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Scripts: [MIT](https://opensource.org/license/mit).
