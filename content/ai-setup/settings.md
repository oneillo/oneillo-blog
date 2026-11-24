---
title: "Settings and hooks"
date: 2026-11-22
lastmod: 2026-11-22
description: "AI-written: the settings and hooks in Orlando's AI setup, described by kind."
ShowReadingTime: false
ShowShareButtons: false
disableShare: true
ShowPostNavLinks: false
ShowBreadCrumbs: true
ShowWordCount: false
hideMeta: true
madeBy: "setup-share"
---

> **Written by AI.** This page was generated from Orlando's own setup by an automated run, and checked for private details before it was published. <a href="/ai-setup/#how-this-section-is-made">How this section is made</a>. Last updated 2026-11-22.

These settings add two hooks, one that runs before a tool is used and one that runs when a session starts, and turn on one plugin and a status line.

## Permissions

The summary gives no default mode and shows no list of things sessions must refuse.

## Plugins and the status line

- Plugin: frontend-design, from the official plugin collection.
- Status line: set up.

Taken out: nothing.

## Hooks

### No em dash {#no-em-dash}

A hook that runs before Claude writes or edits a file and blocks the change if it adds em dashes. It compares the new text with the text it replaces, so em dashes already in a file never trigger it, and it tells Claude to rewrite with other punctuation.

**Works with:** <a href="/ai-setup/apps/#corillo">Corillo</a>.

## <code>no&#45;em&#45;dash&#46;sh</code>

A shell script that runs before a file write or edit and blocks it if the change adds em dashes. It counts em dashes in the new text and the old text, and only newly added ones cause a block.

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere">#!/bin/bash
# PreToolUse hook: block a Write or Edit that adds em dashes.
# Compares the new text with the text it replaces, so em dashes already in a file
# (e.g. Orlando&#x27;s own writing) never trigger it; only newly added ones do.
# Exit 2 blocks the tool call and shows stderr to Claude.
# It also covers Corillo&#x27;s create, replace and edit tools, where Orlando reads
# what agents write. It names those tools: if Corillo renames them, it stops
# checking them silently (update the matcher in settings.json too).

# The em dash character, written as bytes so this file holds none itself.
DASH=$(printf &#x27;\xe2\x80\x94&#x27;)

input=$(cat)
tool=$(echo &quot;$input&quot; | jq -r &#x27;.tool_name // empty&#x27;)
path=$(echo &quot;$input&quot; | jq -r &#x27;.tool_input.file_path // empty&#x27;)

case &quot;$path&quot; in
  &quot;$HOME&quot;/.claude/skills/synced/*|&quot;$HOME&quot;/.claude/hooks/*) exit 0 ;;
esac

count() &#123; jq -r &quot;($1 // \&quot;\&quot;) | [scan(\&quot;\\u2014\&quot;)] | length&quot; &lt;&lt;&lt;&quot;$input&quot;; &#125;

case &quot;$tool&quot; in
  Write)
    new=$(count .tool_input.content)
    old=0
    [ -f &quot;$path&quot; ] &amp;&amp; old=$(grep -o &quot;$DASH&quot; &quot;$path&quot; | wc -l | tr -d &#x27; &#x27;)
    ;;
  Edit)
    new=$(count .tool_input.new_string)
    old=$(count .tool_input.old_string)
    ;;
  mcp__corillo__create_document|mcp__corillo__replace_document)
    path=$(echo &quot;$input&quot; | jq -r &#x27;.tool_input.path // empty&#x27;)
    new=$(count .tool_input.text)
    old=0
    [ -f &quot;$path&quot; ] &amp;&amp; old=$(grep -o &quot;$DASH&quot; &quot;$path&quot; | wc -l | tr -d &#x27; &#x27;)
    ;;
  mcp__corillo__edit_document)
    new=$(count &#x27;([.tool_input.edits[]?.replace // &quot;&quot;] | join(&quot; &quot;))&#x27;)
    old=$(count &#x27;([.tool_input.edits[]?.find // &quot;&quot;] | join(&quot; &quot;))&#x27;)
    ;;
  *) exit 0 ;;
esac

[ &quot;$&#123;new:-0&#125;&quot; -gt &quot;$&#123;old:-0&#125;&quot; ] || exit 0

echo &quot;Blocked: this adds $((new - old)) em dash(es). Rewrite with commas, periods, colons, or parentheses, then retry. Em dashes already in the text you&#x27;re replacing can stay.&quot; &gt;&amp;2
exit 2
</code></pre>

Taken out: nothing.

## Connectors

<a href="/ai-setup/apps/#corillo">Corillo</a> and <a href="/ai-setup/apps/#brega">Brega</a> connect to Claude Code, so a session can use them. Each one has its section on the <a href="/ai-setup/apps/">Apps</a> page.
