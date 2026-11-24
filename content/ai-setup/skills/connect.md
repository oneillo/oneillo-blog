---
title: "Connect"
date: 2026-11-22
lastmod: 2026-11-22
description: "AI-written: a skill that connects new or changed notes in a knowledge base to the rest of it."
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

It links notes to concept notes, creates concept notes for ideas that recur across sources, and adds links between areas.

**Works with:** <a href="/ai-setup/skills/close-shop/">Close shop</a>.

## <code>SKILL&#46;md</code>

The skill's instructions for linking new knowledge base notes to concepts and to each other, used after notes are added or when asked to connect a subject.

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere">---
name: connect
description: Connect new or changed knowledge base notes to concepts and to the rest of the vault. Links notes to concept notes, adds them as sources, creates concept notes for ideas that now recur across sources, and adds cross-area links. Use after adding notes to ~/ai/knowledge-base (close-shop calls it), or when Orlando asks to connect, link or build concepts for a subject.
---

# Connect

The knowledge base stays useful to future agents only if what comes in gets connected: a new book, lecture or post should reach the ideas it discusses, and each idea should gather every source on it. This skill does that work. `kb.py connect` finds it; you judge it.

Read `~/ai/knowledge-base/AGENTS.md` first. Its rules on names, frontmatter, links and writing apply to everything here.

## Scope

- **After adding notes:** the notes you added or changed, plus their subject folders.
- **On demand:** a subject, such as `learning/&lt;subject&gt;`.

Run `uv run ~/ai/tools/scripts/kb.py connect --path &lt;prefix&gt;`. It lists three kinds of work:

1. **Names a concept without linking it:** a note uses a concept&#x27;s name or alias in its text (quotes, Kindle highlights and headings don&#x27;t count: links never go there).
2. **Topic in 2+ notes with no concept note:** a `topics:` value shared by several notes. These are candidates, not orders.
3. **No concept links to it:** in subjects that have concept notes, notes that no concept cites yet.

## 1. Link existing concepts

For each unlinked mention, open the note. Where the text uses the term in the concept&#x27;s sense, even briefly, link the first mention in the body. Leave it unlinked only for another sense of the word, a title, or a place links can&#x27;t go. Change only the link syntax, never Orlando&#x27;s wording: `customer segmentation` becomes `[[market-segmentation|customer segmentation]]`.

Do this with `uv run ~/ai/tools/scripts/link_mentions.py propose ~/ai/tmp/links-&lt;subject&gt;-&lt;time&gt;.md --path &lt;prefix&gt;`: it lists each unlinked mention with every place a link can go, in context, and the words that matched. Write one decision per entry in a text file (`12: 1` links place 1, `13: no &lt;reason&gt;` leaves it), then run `link_mentions.py apply ~/ai/tmp/links-&lt;subject&gt;-&lt;time&gt;.json &lt;decisions file&gt;`, which adds link syntax only. Give each run a file name of its own like this one, so two runs at once never apply one run&#x27;s decisions to the other&#x27;s proposals. Every &quot;no&quot; goes to the vault&#x27;s `_meta/connect-ignore.md` as judged not the concept (another sense, a title, a quote, no place), with its reason and those words, so `kb.py connect` stops listing that pair until the note matches a new word. Read all of an entry&#x27;s places before a &quot;no&quot;: it covers every listed place, and only the words recorded. A mention you judge without the script gets a line there by hand.

For each note in scope, find the concepts it discusses (its `topics`, and the concept names in its subject&#x27;s `concepts/` folder). Link them at the first mention, and add the note to each concept note (step 3).

## 2. Decide which candidates become concept notes

Create a concept note when all of these hold:

- It&#x27;s one idea: a framework, method, principle, model or measure. Not a company, a person, a case, a course or a whole field.
- At least two sources discuss it in substance, not in passing. The topic count is the starting signal; open the sources to confirm.
- Someone asking &quot;what does Orlando know about X?&quot; would want these sources brought together.

Skip the rest. People get notes in `people/` (see AGENTS.md), not concepts. When two candidates are the same idea under different names (&quot;market segmentation&quot;, &quot;customer segmentation&quot;), make one concept with the other as an alias. When a candidate is a narrow case of an existing concept, add it as an alias or a line in that concept instead of a new note.

A large subject can have hundreds of candidates. Work from the top (most shared) down, and stop when the remaining ones are entities or one-off mentions. The leftover list is a backlog, not an error.

## 3. Write or update a concept note

- **Where:** `learning/&lt;subject&gt;/concepts/&lt;name&gt;.md`, in the subject where the idea is most at home. Sources from other subjects link in.
- **Name:** the idea&#x27;s standard name, lowercase-kebab, unique across the vault (`find ~/ai/knowledge-base/ -iname &quot;&lt;name&gt;.*&quot;`).
- **Template:** `~/ai/knowledge-base/_meta/templates/concept.md`.
- **Frontmatter:** `type: concept`, `subject`, `summary`, `source: &quot;Synthesized from the notes listed in sources&quot;`, `sources` (a list of quoted links to every source note), and `aliases`. Aliases are what `kb.py connect` matches in other notes, so use specific phrases the sources actually use, six or more characters, never single common words like &quot;trust&quot; or &quot;power&quot;. A shorter all-caps abbreviation the sources use for the whole idea (&quot;NPV&quot;, &quot;BATNA&quot;) or a short name with a capital letter (&quot;Scrum&quot;) also works: it matches only as written (an abbreviation also as &quot;NPVs&quot; and in &quot;NPV-based&quot;). The title is usually the first alias, so a short capitalized title (&quot;Lean&quot;) matches as written too, sentence starts included. To stop that, keep it out of `aliases` and put it in `title:` instead, as `flow-state` does with &quot;Flow&quot;.
- **Body:**
  1. What the idea is, in a sentence or two, then how it works and when it applies. Build it only from the sources. No facts from your own knowledge; mark gaps `[?]`.
  2. `## Where it comes up`: one bullet per source: a link to the most specific place (a highlight block `[[&lt;source-note&gt;#^loc-1469]]`, a heading `[[&lt;source-note&gt;#Targeting]]`, or the note) and a sentence on what that source adds. Where sources disagree, say so. Quote at most a short phrase; the full quotes stay in the source notes.
  3. `## Related`: broader, narrower and opposing concepts, linked.
- **Updating an existing concept:** add the new source to `sources` and to &quot;Where it comes up&quot;, and revise the explanation if the source adds something. Keep what&#x27;s there.

## 4. Cross-area links

The graph is only as connected as the links across areas. For the notes in scope, search Orlando&#x27;s writing and career for the same book, author or idea:

```
grep -rli &quot;&lt;distinctive phrase&gt;\|&lt;author surname&gt;&quot; ~/ai/knowledge-base/writing/blog/archive ~/ai/knowledge-base/career ~/ai/knowledge-base/self-knowledge ~/ai/knowledge-base/people
```

Link both ways at the first mention, when the connection is real. A blog post reviewing a book, a career story that used a framework, a person who taught a course.

- Don&#x27;t edit blog drafts in `writing/blog/` outside `archive/`: the publish script reads them.
- [A list of private folders is left out here.]

## 5. Finish

1. `uv run ~/ai/tools/scripts/kb.py hubs --path &lt;subject&gt;`: a new `concepts/` folder is listed in the subject&#x27;s hub.
2. `uv run ~/ai/tools/scripts/kb.py check --path &lt;prefix&gt;`: fix what you introduced.
3. `uv run ~/ai/tools/scripts/kb.py connect --path &lt;prefix&gt;`: &quot;names a concept without linking it&quot; should be empty for your scope (mentions you judged not the concept are on the ignore list).
4. Say in a few lines what you did: concepts created and updated, links added, and any candidate you deliberately skipped that someone might expect.

## Working in parallel

For a big subject, agents can split the candidates, but only one agent edits a given concept note or a given source note at a time. Give each agent its own list of concepts, and collect links into other agents&#x27; notes in a report for one agent to apply.
</code></pre>

Taken out: 1 course code, 2 private example note names, and 1 list of private folders.
