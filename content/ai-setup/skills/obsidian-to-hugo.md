---
title: "Obsidian to Hugo"
date: 2026-11-22
lastmod: 2026-11-22
description: "AI-written: a skill that converts a blog post written in Obsidian into Hugo-ready Markdown and places it in a review folder."
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

A script does the conversion, links to existing posts are kept, other links become plain text with a warning, and pictures are copied next to the post.

This skill is specific to Orlando's blog. It is here because it is part of his setup, and its steps may still be worth borrowing.

**Works with:** <a href="/ai-setup/apps/#corillo">Corillo</a> and <a href="/ai-setup/skills/relay/">Relay</a>.

## <code>SKILL&#46;md</code>

The instructions for converting an Obsidian blog post to Hugo Markdown, covering how to find the post, run the conversion script, report the result, and handle errors.

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere">---
name: obsidian-to-hugo
description: Convert an Obsidian blog post to Hugo-compatible markdown and place it in the blog projects folder for review
allowed-tools: Read, Bash, Glob, Grep, AskUserQuestion
---

# Obsidian to Hugo Converter

Convert a blog post written in Obsidian to Hugo-compatible markdown and place it in `~/ai/projects/blog/` for editing and review.

## Paths

- **Obsidian source**: `&lt;vault-folder&gt;/writing/blog/`
- **Destination**: `~/ai/projects/blog/posts-to-review/`

If the Obsidian path does not exist, ask the user for the correct vault location.

## Steps

### 1. Identify the source file

If the user specified a filename (as an argument or in conversation), use that. Otherwise, list the markdown files in the Obsidian source directory and ask which one to convert.

Match flexibly -- the user might give a partial name, a slug, or a title. Find the closest match in the source directory.

### 2. Run the conversion

Run the conversion script:

```bash
uv run ~/.claude/skills/obsidian-to-hugo/scripts/convert.py --file &quot;&lt;filename&gt;&quot; [--verbose]
```

Run it without asking. If it says the converted file already exists, ask before running again with `--force`: the copy may hold review edits. The script does not modify the original Obsidian file.

A `[[link]]` becomes a link only when the post exists in `&lt;blog-folder&gt;/content/posts/`; any other link (a note, a heading) is kept as text without a link, with a warning. Pictures, both `![[...]]` embeds and standard links such as Corillo&#x27;s, are copied next to the converted post.

Tests: `uv run --offline ~/.claude/skills/obsidian-to-hugo/scripts/test_convert.py`.

### 3. Report the result

After the script completes successfully, tell the user where the converted file landed. One line is enough. Example:

&gt; Converted to `~/ai/projects/blog/posts-to-review/my-post-title/my-post-title.md`

If the script reported any warnings (missing images, links kept as text), relay those.

### 4. Errors

If the script fails, show the error output and suggest a fix. Common issues:
- Source file not found -- check the filename and list available files
- Destination directory missing -- this shouldn&#x27;t happen, but create it if needed
</code></pre>

## Scripts

Listed by name, with one line each. Their code is not shared.

- <code>convert&#46;py</code>: Obsidian to Hugo Converter
- <code>test&#95;convert&#46;py</code>: Tests for convert.py, on made-up posts in a temp folder.

Taken out: 2 paths.
