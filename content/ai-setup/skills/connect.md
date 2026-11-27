---
title: "Connect: a Claude Code skill that links notes to the ideas they discuss"
date: 2026-11-22
lastmod: 2026-11-27
summary: "AI-written: Links new knowledge base notes to concept notes, creates concept notes for recurring ideas, and adds cross-area links."
heading: "Connect"
linkTitle: "Connect"
author: "Orlando O'Neill"
images: ["/ai-setup/cards/skills/connect.png"]
ShowReadingTime: false
ShowShareButtons: false
disableShare: true
ShowPostNavLinks: false
ShowBreadCrumbs: true
ShowWordCount: false
hideMeta: true
madeBy: "setup-share"
---

<div class="post-description">Links new knowledge base notes to concept notes, creates concept notes for recurring ideas, and adds cross-area links.</div>

<p class="ai-setup-label">Written by AI from Orlando's setup. Last updated <time datetime="2026-11-27">2026-11-27</time>. Also changed: <time datetime="2026-11-22">2026-11-22</time>. <a href="/ai-setup/#how-this-section-is-made">How this is made</a>.</p>

<section class="facts facts-skill">

<div class="facts-head"><span class="tag tag-skill">Skill</span><span class="facts-cmd"><code>/connect</code></span></div>

- **What it is:** Connect is a Claude Code skill that links new or changed notes in a knowledge base to concept notes and builds concept notes for ideas that recur.
- **How it starts:** You type <code>&#47;connect</code>. Claude can also pick it up when it fits.
- **Where the file goes:** <code>&#126;&#47;&#46;claude&#47;skills&#47;connect&#47;SKILL&#46;md</code>
- **Needs:** uv
- **To make it yours:** Put your own values where you see <code>&#60;area&#62;</code>, <code>&#60;course&#45;note&#62;</code>, <code>&#60;source&#45;note&#62;</code>, and <code>&#60;vault&#45;folder&#62;</code>.
  Taken out for privacy: 2 lists of private folders, 2 paths, and 2 note names.

<div class="works">

**Works with:**
- **Also mentions:** <a href="/ai-setup/skills/close-shop/">Close shop</a>.

</div>

</section>

<section class="file">

## The skill file (<code>SKILL&#46;md</code>) {#skill-md}

<a href="#skill-md">Link to this file</a>

The instructions for linking new or changed knowledge base notes to concept notes, creating concept notes for ideas that recur, and adding cross-area links. It is used after notes are added or when a subject needs connecting.

<figure class="code">
<div class="code-bar"><span class="code-name">SKILL.md</span><span class="code-meta">Markdown · 87 lines</span><button type="button" class="ai-setup-copy copy" hidden>Copy</button></div>

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere">---
name: connect
description: Links new or changed knowledge base notes to concept notes (adding them as sources), creates concept notes for ideas that now recur across sources, and adds cross-area links. Use after adding notes to ~/ai/knowledge-base (close-shop calls it), or when Orlando asks to connect, link or build concepts for a subject.
---

# Connect

Goal: each new source reaches the ideas it discusses, and each idea gathers every source on it. `kb.py connect` finds the work; you judge it.

Read `~/ai/knowledge-base/AGENTS.md` first: its rules on names, frontmatter, links and writing apply here.

## Scope

- **After adding notes:** the notes added or changed, plus their subject folders.
- **On demand:** a subject, such as `learning/<span class="ph">&lt;subject&gt;</span>`.

Run `uv run ~/ai/tools/scripts/kb.py connect --path <span class="ph">&lt;prefix&gt;</span>`. It lists:

1. **Names a concept without linking it:** a note uses a concept&#x27;s name or alias in its text (quotes, Kindle highlights and headings don&#x27;t count: links never go there).
2. **Topic in 2+ notes with no concept note:** a shared `topics:` value. Candidates, not orders.
3. **No concept links to it:** in subjects with concept notes, notes no concept cites yet.

## 1. Link existing concepts

Open the note for each unlinked mention. Where the text uses the term in the concept&#x27;s sense, even briefly, link the first mention in the body. Leave it unlinked only for another sense of the word, a title, or a place links can&#x27;t go. Change only link syntax, never Orlando&#x27;s wording: `customer segmentation` becomes `[[market-segmentation|customer segmentation]]`.

Use the script:

1. `uv run ~/ai/tools/scripts/link_mentions.py propose ~/ai/tmp/links-<span class="ph">&lt;subject&gt;</span>-&lt;time&gt;.md --path <span class="ph">&lt;prefix&gt;</span>`: lists each unlinked mention with every place a link can go, in context, and the matched words.
2. Write one decision per entry in a text file: `12: 1` links place 1, `13: no <span class="ph">&lt;reason&gt;</span>` leaves it. Read all of an entry&#x27;s places before a &quot;no&quot;: it covers every listed place, and only the words recorded.
3. `link_mentions.py apply ~/ai/tmp/links-<span class="ph">&lt;subject&gt;</span>-&lt;time&gt;.json <span class="ph">&lt;decisions file&gt;</span>`: adds link syntax only.

Each run gets its own file name like this, so two runs at once never cross decisions and proposals. Every &quot;no&quot; goes to the vault&#x27;s `_meta/connect-ignore.md` as judged not the concept (another sense, a title, a quote, no place), with its reason and words, so `kb.py connect` stops listing that pair until the note matches a new word. A mention judged without the script gets a line there by hand.

For each note in scope, find the concepts it discusses (its `topics`, and the names in its subject&#x27;s `concepts/` folder). Link them at first mention, and add the note to each concept note (step 3).

## 2. Decide which candidates become concept notes

Create one only when all hold:

- One idea: a framework, method, principle, model or measure. Not a company, person, case, course or whole field.
- At least two sources discuss it in substance, not in passing. The topic count is the starting signal; open the sources to confirm.
- Someone asking &quot;what does Orlando know about X?&quot; would want these sources together.

Skip the rest. People get notes in `people/` (see AGENTS.md), not concepts. Two names for one idea (&quot;market segmentation&quot;, &quot;customer segmentation&quot;): one concept, the other as an alias. A narrow case of an existing concept: an alias or a line in that concept, not a new note.

A large subject can have hundreds of candidates. Work from the most shared down; stop when the rest are entities or one-off mentions. The leftover list is a backlog, not an error.

## 3. Write or update a concept note

- **Where:** `learning/<span class="ph">&lt;subject&gt;</span>/concepts/<span class="ph">&lt;name&gt;</span>.md`, in the subject where the idea is most at home. Sources from other subjects link in.
- **Name:** the idea&#x27;s standard name, lowercase-kebab, unique across the vault (`find ~/ai/knowledge-base/ -iname &quot;<span class="ph">&lt;name&gt;</span>.*&quot;`).
- **Template:** `~/ai/knowledge-base/_meta/templates/concept.md`.
- **Frontmatter:** `type: concept`, `subject`, `summary`, `source: &quot;Synthesized from the notes listed in sources&quot;`, `sources` (quoted links to every source note), `aliases`.
- **Aliases** are what `kb.py connect` matches in other notes:
  - Specific phrases the sources actually use, six or more characters. Never single common words like &quot;trust&quot; or &quot;power&quot;.
  - Also fine: a shorter all-caps abbreviation the sources use for the whole idea (&quot;NPV&quot;, &quot;BATNA&quot;) or a short capitalized name (&quot;Scrum&quot;). These match only as written (an abbreviation also as &quot;NPVs&quot; and in &quot;NPV-based&quot;).
  - The title is usually the first alias, so a short capitalized title (&quot;Lean&quot;) matches as written too, sentence starts included. To stop that, keep it out of `aliases` and put it in `title:`, as `flow-state` does with &quot;Flow&quot;.
- **Body:**
  1. What the idea is in a sentence or two, then how it works and when it applies. Only from the sources, nothing from your own knowledge; mark gaps `[?]`.
  2. `## Where it comes up`: one bullet per source, linking the most specific place (a highlight block `[[<span class="ph">&lt;source-note&gt;</span>#^loc-1469]]`, a heading `[[<span class="ph">&lt;course-note&gt;</span>#Targeting]]`, or the note), with a sentence on what it adds. Say where sources disagree. Quote at most a short phrase; full quotes stay in the source notes.
  3. `## Related`: broader, narrower and opposing concepts, linked.
- **Updating:** add the new source to `sources` and &quot;Where it comes up&quot;, and revise the explanation if it adds something. Keep what&#x27;s there.

## 4. Cross-area links

For the notes in scope, search Orlando&#x27;s writing and other areas for the same book, author or idea:

```
grep -rli &quot;<span class="ph">&lt;distinctive phrase&gt;</span>\|<span class="ph">&lt;author surname&gt;</span>&quot; <span class="ph">&lt;vault-folder&gt;</span>/<span class="ph">&lt;area&gt;</span> <span class="ph">&lt;vault-folder&gt;</span>/<span class="ph">&lt;area&gt;</span>
```

When the connection is real (a blog post reviewing a book, a career story that used a framework, a person who taught a course), link both ways at the first mention.

- Don&#x27;t edit blog drafts outside the archive: the publish script reads them.
- [A list of private folders is left out here.]

## 5. Finish

1. `uv run ~/ai/tools/scripts/kb.py hubs --path <span class="ph">&lt;subject&gt;</span>`: lists a new `concepts/` folder in the subject&#x27;s hub.
2. `uv run ~/ai/tools/scripts/kb.py check --path <span class="ph">&lt;prefix&gt;</span>`: fix what you introduced.
3. `uv run ~/ai/tools/scripts/kb.py connect --path <span class="ph">&lt;prefix&gt;</span>`: &quot;names a concept without linking it&quot; should be empty for your scope (mentions judged not the concept are on the ignore list).
4. Report in a few lines: concepts created and updated, links added, and any candidate deliberately skipped that someone might expect.

## Working in parallel

Agents can split a big subject&#x27;s candidates, but only one agent edits a given concept or source note at a time. Give each agent its own concept list; links into other agents&#x27; notes go in a report for one agent to apply.
</code></pre>

</figure>

</section>

<nav class="page-foot">

Previous: <a href="/ai-setup/skills/tune-setup/">Tune setup</a>.
Next: <a href="/ai-setup/skills/publish-setup/">Publish setup</a>.

<a href="/ai-setup/skills/">Back to Skills</a>

<a href="https://www.linkedin.com/in/oneillo/" target="_blank" rel="noopener">Borrowed something? Tell me how it went</a>

Text: <a href="https://creativecommons.org/licenses/by/4.0/" target="_blank" rel="license noopener">CC BY 4.0</a>. Scripts: <a href="https://opensource.org/license/mit" target="_blank" rel="license noopener">MIT</a>.

</nav>
