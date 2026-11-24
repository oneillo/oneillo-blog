---
title: "Publish prep: a Claude Code skill that prepares a finished blog post to publish"
date: 2026-11-22
lastmod: 2026-11-22
summary: "AI-written: Prepares a finished blog post for publishing with title options, a description, a LinkedIn blurb, and a Substack version."
heading: "Publish prep"
linkTitle: "Publish prep"
author: "Orlando O'Neill"
images: ["/ai-setup/cards/skills/publish-prep.png"]
ShowReadingTime: false
ShowShareButtons: false
disableShare: true
ShowPostNavLinks: false
ShowBreadCrumbs: true
ShowWordCount: false
hideMeta: true
madeBy: "setup-share"
---

<div class="post-description">Prepares a finished blog post for publishing with title options, a description, a LinkedIn blurb, and a Substack version.</div>

> **Written by AI.** This page was generated from Orlando's own setup by an automated run, and checked for private details before it was published. <a href="/ai-setup/#how-this-section-is-made">How this section is made</a>. Last updated 2026-11-22.

- **What it is:** Publish prep is a Claude Code skill that prepares a finished blog post for publishing by drafting its title, description, LinkedIn blurb, and Substack version.
- **How it starts:** You type <code>&#47;publish&#45;prep</code>. Claude doesn't start it by itself.
- **Where the file goes:** <code>&#126;&#47;&#46;claude&#47;skills&#47;publish&#45;prep&#47;SKILL&#46;md</code>
- **Needs:** Nothing extra

This skill is specific to Orlando's blog. It is here because it is part of his setup, and its steps may still be worth borrowing.

**Works with:** <a href="/ai-setup/skills/publish-post/">Publish post</a>.

## The skill file (<code>SKILL&#46;md</code>) {#skill-md}

<a href="#skill-md">Link to this file</a>

The instructions for preparing a finished blog post for publishing, step by step, with the author approving each piece. It is used after a post is written and before it goes live.

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere">---
name: publish-prep
description: &quot;Prepare a finished blog post for publishing: generate title, description, LinkedIn blurb, and Substack version.&quot;
disable-model-invocation: true
argument-hint: [path to post directory]
---

# Publish Prep

Prepare a finished blog post for publishing by generating its title, description, LinkedIn blurb, and Substack version. The author (Orlando) makes final selections and approvals at each step.

## Inputs

- **Post directory path**: provided as `$ARGUMENTS`. Contains the finished blog post draft and any supporting files (outline, images, etc.).
- **Series plan**: if the post is part of a series, read the series plan for context on the arc and how prior posts were titled/described.
- **Prior post artifacts**: read title/description, LinkedIn, and Substack files from prior posts in the same series (or from recent posts if standalone) to calibrate tone and format.
- **Style guide**: read `~/ai/knowledge-base/writing/writing-style-guide.md` for voice and tone reference.

## Steps

### Step 1: Gather context

1. Read the finished blog post.
2. Read the series plan if one exists in the project directory.
3. Find and read title/description, LinkedIn, and Substack files from at least two prior posts for calibration.
4. Read the style guide.

### Step 2: Title and description

1. Generate 5-7 title candidates. Vary the approach (direct, question-based, provocative, pain-point-led). Explain the angle behind each.
2. Generate 2-3 description candidates. Each should be one sentence, practical, and hint at the problem being solved.
3. Present all options to the author.

**Quality gate:** Before presenting, verify:
- Every title is under 70 characters
- Titles and descriptions are consistent with the series naming pattern (if applicable)
- Each option could work on social media and reflects Orlando&#x27;s conversational tone

4. Wait for the author to select a title and description.
5. Save the selection to `post-N-title-and-description.md` in the post directory using this format:

```
# Post N: Title and Description

**Title:** [selected title]

**Description:** [selected description]
```

### Step 3: LinkedIn blurb

1. Draft a LinkedIn blurb. It should sound like a person sharing something they wrote, not marketing:
   - Plain, first-person, low-key: what the post is, why he wrote it, maybe one line on what&#x27;s in it
   - Reference the series by name if part of one
   - No hooks, benefit-led openers, or stacked selling points
2. Present to the author for approval.

**Quality gate:** Before presenting, verify:
- Under 600 characters (roughly the fold on mobile LinkedIn)
- Consistent with the title and description in how it frames the post

3. Save to `post-N-linkedin.md` in the post directory.

### Step 4: Substack version

1. Draft a Substack version of the post. This is NOT a truncation or teaser. It must:
   - Teach the post&#x27;s core concepts at a high level, enough to deliver standalone value
   - Be useful to a reader who never clicks through to the blog
   - Reserve the concrete artifacts (examples, code excerpts, reusable prompts, detailed walkthroughs) as the draw to the full blog post
   - End with a clear link to the full post: `[Read the full post here →](BLOG_URL)`
   - Use `BLOG_URL` as a placeholder; `/publish-post` fills in the address after publishing
2. Present to the author for approval.

**Quality gate:** Before presenting, verify:
- The Substack version delivers value as standalone content, not just a teaser
- It does not duplicate large sections of the blog post verbatim
- It is consistent with the title, description, and LinkedIn blurb in framing

3. Save to `post-N-substack.md` in the post directory.

### Step 5: Confirm completion

List the files created with their paths so the author can verify everything is saved.
</code></pre>
