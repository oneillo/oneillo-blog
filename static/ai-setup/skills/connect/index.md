# Connect

- Kind: Claude Code skill
- Address: https://oneillo.com/ai-setup/skills/connect/
- Last updated: 2026-11-27
- Also changed: 2026-11-22

> AI-written and cleaned from one person's working setup. Placeholders in angle brackets are not real values: ask your user for theirs. The newest version is at https://oneillo.com/ai-setup/skills/connect/.

Links new knowledge base notes to concept notes, creates concept notes for recurring ideas, and adds cross-area links.

- **What it is:** Connect is a Claude Code skill that links new or changed notes in a knowledge base to concept notes and builds concept notes for ideas that recur.
- **How it starts:** You type `/connect`. Claude can also pick it up when it fits.
- **Where the file goes:** `~/.claude/skills/connect/SKILL.md`
- **Needs:** uv
- **To make it yours:** Put your own values where you see `<area>`, `<course-note>`, `<source-note>`, and `<vault-folder>`.
  Taken out for privacy: 2 lists of private folders, 2 paths, and 2 note names.

**Works with:**
- **Also mentions:** [Close shop](https://oneillo.com/ai-setup/skills/close-shop/).

## The skill file (`SKILL.md`)

The instructions for linking new or changed knowledge base notes to concept notes, creating concept notes for ideas that recur, and adding cross-area links. It is used after notes are added or when a subject needs connecting.

````
---
name: connect
description: Links new or changed knowledge base notes to concept notes (adding them as sources), creates concept notes for ideas that now recur across sources, and adds cross-area links. Use after adding notes to ~/ai/knowledge-base (close-shop calls it), or when Orlando asks to connect, link or build concepts for a subject.
---

# Connect

Goal: each new source reaches the ideas it discusses, and each idea gathers every source on it. `kb.py connect` finds the work; you judge it.

Read `~/ai/knowledge-base/AGENTS.md` first: its rules on names, frontmatter, links and writing apply here.

## Scope

- **After adding notes:** the notes added or changed, plus their subject folders.
- **On demand:** a subject, such as `learning/<subject>`.

Run `uv run ~/ai/tools/scripts/kb.py connect --path <prefix>`. It lists:

1. **Names a concept without linking it:** a note uses a concept's name or alias in its text (quotes, Kindle highlights and headings don't count: links never go there).
2. **Topic in 2+ notes with no concept note:** a shared `topics:` value. Candidates, not orders.
3. **No concept links to it:** in subjects with concept notes, notes no concept cites yet.

## 1. Link existing concepts

Open the note for each unlinked mention. Where the text uses the term in the concept's sense, even briefly, link the first mention in the body. Leave it unlinked only for another sense of the word, a title, or a place links can't go. Change only link syntax, never Orlando's wording: `customer segmentation` becomes `[[market-segmentation|customer segmentation]]`.

Use the script:

1. `uv run ~/ai/tools/scripts/link_mentions.py propose ~/ai/tmp/links-<subject>-<time>.md --path <prefix>`: lists each unlinked mention with every place a link can go, in context, and the matched words.
2. Write one decision per entry in a text file: `12: 1` links place 1, `13: no <reason>` leaves it. Read all of an entry's places before a "no": it covers every listed place, and only the words recorded.
3. `link_mentions.py apply ~/ai/tmp/links-<subject>-<time>.json <decisions file>`: adds link syntax only.

Each run gets its own file name like this, so two runs at once never cross decisions and proposals. Every "no" goes to the vault's `_meta/connect-ignore.md` as judged not the concept (another sense, a title, a quote, no place), with its reason and words, so `kb.py connect` stops listing that pair until the note matches a new word. A mention judged without the script gets a line there by hand.

For each note in scope, find the concepts it discusses (its `topics`, and the names in its subject's `concepts/` folder). Link them at first mention, and add the note to each concept note (step 3).

## 2. Decide which candidates become concept notes

Create one only when all hold:

- One idea: a framework, method, principle, model or measure. Not a company, person, case, course or whole field.
- At least two sources discuss it in substance, not in passing. The topic count is the starting signal; open the sources to confirm.
- Someone asking "what does Orlando know about X?" would want these sources together.

Skip the rest. People get notes in `people/` (see AGENTS.md), not concepts. Two names for one idea ("market segmentation", "customer segmentation"): one concept, the other as an alias. A narrow case of an existing concept: an alias or a line in that concept, not a new note.

A large subject can have hundreds of candidates. Work from the most shared down; stop when the rest are entities or one-off mentions. The leftover list is a backlog, not an error.

## 3. Write or update a concept note

- **Where:** `learning/<subject>/concepts/<name>.md`, in the subject where the idea is most at home. Sources from other subjects link in.
- **Name:** the idea's standard name, lowercase-kebab, unique across the vault (`find ~/ai/knowledge-base/ -iname "<name>.*"`).
- **Template:** `~/ai/knowledge-base/_meta/templates/concept.md`.
- **Frontmatter:** `type: concept`, `subject`, `summary`, `source: "Synthesized from the notes listed in sources"`, `sources` (quoted links to every source note), `aliases`.
- **Aliases** are what `kb.py connect` matches in other notes:
  - Specific phrases the sources actually use, six or more characters. Never single common words like "trust" or "power".
  - Also fine: a shorter all-caps abbreviation the sources use for the whole idea ("NPV", "BATNA") or a short capitalized name ("Scrum"). These match only as written (an abbreviation also as "NPVs" and in "NPV-based").
  - The title is usually the first alias, so a short capitalized title ("Lean") matches as written too, sentence starts included. To stop that, keep it out of `aliases` and put it in `title:`, as `flow-state` does with "Flow".
- **Body:**
  1. What the idea is in a sentence or two, then how it works and when it applies. Only from the sources, nothing from your own knowledge; mark gaps `[?]`.
  2. `## Where it comes up`: one bullet per source, linking the most specific place (a highlight block `[[<source-note>#^loc-1469]]`, a heading `[[<course-note>#Targeting]]`, or the note), with a sentence on what it adds. Say where sources disagree. Quote at most a short phrase; full quotes stay in the source notes.
  3. `## Related`: broader, narrower and opposing concepts, linked.
- **Updating:** add the new source to `sources` and "Where it comes up", and revise the explanation if it adds something. Keep what's there.

## 4. Cross-area links

For the notes in scope, search Orlando's writing and other areas for the same book, author or idea:

```
grep -rli "<distinctive phrase>\|<author surname>" <vault-folder>/<area> <vault-folder>/<area>
```

When the connection is real (a blog post reviewing a book, a career story that used a framework, a person who taught a course), link both ways at the first mention.

- Don't edit blog drafts outside the archive: the publish script reads them.
- [A list of private folders is left out here.]

## 5. Finish

1. `uv run ~/ai/tools/scripts/kb.py hubs --path <subject>`: lists a new `concepts/` folder in the subject's hub.
2. `uv run ~/ai/tools/scripts/kb.py check --path <prefix>`: fix what you introduced.
3. `uv run ~/ai/tools/scripts/kb.py connect --path <prefix>`: "names a concept without linking it" should be empty for your scope (mentions judged not the concept are on the ignore list).
4. Report in a few lines: concepts created and updated, links added, and any candidate deliberately skipped that someone might expect.

## Working in parallel

Agents can split a big subject's candidates, but only one agent edits a given concept or source note at a time. Give each agent its own concept list; links into other agents' notes go in a report for one agent to apply.
````

Previous: [Tune setup](https://oneillo.com/ai-setup/skills/tune-setup/).
Next: [Publish setup](https://oneillo.com/ai-setup/skills/publish-setup/).

[Back to Skills](https://oneillo.com/ai-setup/skills/)

[Borrowed something? Tell me how it went](https://www.linkedin.com/in/oneillo/)

Text: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Scripts: [MIT](https://opensource.org/license/mit).
