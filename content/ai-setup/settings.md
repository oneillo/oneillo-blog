---
title: "Settings and hooks: how Orlando's Claude Code is set up"
date: 2026-11-22
lastmod: 2026-11-23
summary: "AI-written: the settings and hooks in Orlando's AI setup, described by kind."
heading: "Settings and hooks"
linkTitle: "Settings and hooks"
author: "Orlando O'Neill"
images: ["/ai-setup/cards/settings.png"]
ShowReadingTime: false
ShowShareButtons: false
disableShare: true
ShowPostNavLinks: false
ShowBreadCrumbs: true
ShowWordCount: false
hideMeta: true
madeBy: "setup-share"
---

<div class="post-description">The settings and hooks in Orlando's AI setup, described by kind.</div>

<p class="ai-setup-label">Written by AI from Orlando's setup. Last updated <time datetime="2026-11-23">2026-11-23</time>. <a href="/ai-setup/#how-this-section-is-made">How this is made</a>.</p>

Sets up hooks, a plugin, a status line, and permissions so Claude Code sessions start and run the way the setup expects.

Two hooks run before tools are used and when a session starts. The frontend-design plugin is turned on.

## Permissions

There is a default mode setting left open, and no list of things sessions must refuse.

## Plugins and the status line

One plugin is turned on: frontend-design. The status line shows the model, the folder, and how much of the context window is used.

## Hooks

The settings set up 2 hooks: commands Claude Code runs at set points in a session.

### No em dash {#no-em-dash}

<section class="facts facts-hook">

<div class="facts-head"><span class="tag tag-hook">Hook</span></div>

- **Where the file goes:** <code>&#126;&#47;&#46;claude&#47;hooks&#47;no&#45;em&#45;dash&#46;sh</code>, set up in <code>&#126;&#47;&#46;claude&#47;settings&#46;json</code>

</section>

Blocks file writes and edits that add em dashes, while leaving em dashes already in the text alone.

<div class="works">

**Works with:**
- **Also mentions:** <a href="/ai-setup/apps/#corillo">Corillo</a>.

</div>

<section class="file">

## The hook script (<code>no&#45;em&#45;dash&#46;sh</code>) {#no-em-dash-sh}

<a href="#no-em-dash-sh">Link to this file</a>

A hook script that runs before a write or edit tool call and blocks it if the new text has more em dashes than the text it replaces. It tells Claude to rewrite with other punctuation.

<figure class="code">
<div class="code-bar"><span class="code-name">no-em-dash.sh</span><span class="code-meta">Shell · 46 lines</span><button type="button" class="ai-setup-copy copy" hidden>Copy</button></div>

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere">#!/bin/bash
# PreToolUse hook: block a Write or Edit that adds em dashes.
# Compares the new text with the text it replaces, so em dashes already in a file
# (e.g. Orlando&#x27;s own writing) never trigger it; only newly added ones do.
# Exit 2 blocks the tool call and shows stderr to Claude.
# It also covers Corillo&#x27;s create, replace and edit tools, where Orlando reads
# what agents write. It names those tools: if Corillo renames them, it stops
# checking them silently (update the matcher in settings.json too).

input=$(cat)
tool=$(echo &quot;$input&quot; | jq -r &#x27;.tool_name // empty&#x27;)
path=$(echo &quot;$input&quot; | jq -r &#x27;.tool_input.file_path // empty&#x27;)

case &quot;$path&quot; in
  &quot;$HOME&quot;/.claude/skills/synced/*|&quot;$HOME&quot;/.claude/hooks/*) exit 0 ;;
esac

count() &#123; jq -r &quot;($1 // \&quot;\&quot;) | [scan(\&quot;—\&quot;)] | length&quot; &lt;&lt;&lt;&quot;$input&quot;; &#125;

case &quot;$tool&quot; in
  Write)
    new=$(count .tool_input.content)
    old=0
    [ -f &quot;$path&quot; ] &amp;&amp; old=$(grep -o &#x27;—&#x27; &quot;$path&quot; | wc -l | tr -d &#x27; &#x27;)
    ;;
  Edit)
    new=$(count .tool_input.new_string)
    old=$(count .tool_input.old_string)
    ;;
  mcp__corillo__create_document|mcp__corillo__replace_document)
    path=$(echo &quot;$input&quot; | jq -r &#x27;.tool_input.path // empty&#x27;)
    new=$(count .tool_input.text)
    old=0
    [ -f &quot;$path&quot; ] &amp;&amp; old=$(grep -o &#x27;—&#x27; &quot;$path&quot; | wc -l | tr -d &#x27; &#x27;)
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

</figure>

</section>

## Connectors

<a href="/ai-setup/apps/#corillo">Corillo</a> and <a href="/ai-setup/apps/#brega">Brega</a> connect to Claude Code, so a session can use them. Each one has its section on the <a href="/ai-setup/apps/">Apps</a> page.

<nav class="page-foot">

<a href="/ai-setup/">Back to AI Setup</a>

<a href="https://www.linkedin.com/in/oneillo/" target="_blank" rel="noopener">Borrowed something? Tell me how it went</a>

Text: <a href="https://creativecommons.org/licenses/by/4.0/" target="_blank" rel="license noopener">CC BY 4.0</a>. Scripts: <a href="https://opensource.org/license/mit" target="_blank" rel="license noopener">MIT</a>.

</nav>
