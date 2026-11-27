# Podcast

- Kind: Claude Code skill
- Address: https://oneillo.com/ai-setup/skills/podcast/
- Last updated: 2026-11-27
- Also changed: 2026-11-22

> AI-written and cleaned from one person's working setup. Placeholders in angle brackets are not real values: ask your user for theirs. The newest version is at https://oneillo.com/ai-setup/skills/podcast/.

Turns source documents into a two-host podcast MP3, writing the script and rendering the voices on the Mac.

- **What it is:** Podcast is a Claude Code skill that turns source documents into a two-host MP3 episode, with the script written and the voices rendered locally.
- **How it starts:** You type `/podcast`. Claude can also pick it up when it fits.
- **Where the file goes:** `~/.claude/skills/podcast/SKILL.md`
- **Needs:** Kokoro, uv, and Apple Music
- **To make it yours:** Put your own values where you see `<album-name>`.
  Taken out for privacy: 1 family detail and 1 career detail.

## The skill file (`SKILL.md`)

The main instructions for the podcast skill: how to read the sources, write a two-host script, render it to an MP3 with Kokoro, and hand it off.

````
---
name: podcast
description: Turns source documents into a two-host, podcast-style MP3 for Orlando to listen to while exercising, commuting or doing chores. Writes the script and renders it locally with Kokoro. Use when he asks for a podcast, an episode or album to listen to, or an audio version of documents or a topic.
argument-hint: [sources (files or folder)] [length and focus, optional]
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, AskUserQuestion
---

# Podcast

Turns material into a two-host conversation rendered to MP3 on his Mac. No cloud TTS, no accounts. Render scripts: `~/ai/tools/podcast/`.

## 1. Inputs

- **Sources:** files or a folder from the arguments or conversation. Read them fully. On conflicts, go with the most recent, well-sourced one and tell Orlando in chat (not in the audio).
- **Length:** a cap, not a target. Say what the material needs and stop: never pad to reach a length, and a short episode is fine. Keep each episode under about 25 minutes (about 3,750 words at 150 spoken words a minute) so he can hear it in one sitting: the Music app loses his place once he plays anything else. If the material needs more, split it into episodes that each stand on their own. Honor a length he asks for.
- **Focus:** what it's for (learning a topic, a book summary). Infer it; ask one question only if you can't.

## 2. Write the script

Save to `<project>/podcast/<slug>-script.md`, `<project>` being the folder the sources belong to (create `podcast/` if needed).

```
---
title: "The big picture"
album: "<album-name>"
track: 1
sources: ...
pronounce: GIF=jif; SQL=sequel
---

A: First line from the lead host.
B: Co-host reply.
```

- One turn per line: `A:` (lead host, knows the material) or `B:` (co-host, asks what Orlando would ask and pushes back). Only `A:`/`B:` lines are spoken.
- `pronounce:` maps words the voices may get wrong (names, brands, homographs, anything unusual) to spellings that sound right.
- `title:`, `album:`, `track:` become MP3 tags, so Apple Music shows each set as one album by the artist Claude AI (the renderer sets the artist). Short title, since the album carries the series name. One album per set, the same on every episode, tracks numbered in listening order. A set of take twos gets its own album ("..., take two").

Writing for the ear:
- Open with why the episode matters and the single most important takeaway. Close with a recap and anything Orlando can think through while he listens.
- Address the listener as "you." Turns of 1-4 sentences; a lone "Why?" or "Got it." is fine.
- Numbers as said: "about eight point three billion dollars," "a hundred twenty-four thousand," "fiscal twenty-eight." Round where precision doesn't matter.
- Acronyms spelled out loud as spaced capitals ("G P M," "O K Rs"); ones said as words written normally ("NASA").
- Plain sentences only: no markdown, bullets, parentheses, URLs or em dashes.
- Facts only from the sources. Say "unverified" or "an estimate" out loud when the source does. Paraphrase rather than read long quotes.
- Signpost sections ("Okay, culture.") so structure survives without visuals.
- Listen-only: no rehearsal pauses, silent gaps or prompts to practice out loud: he listens to remember the material. If he needs to rehearse, give him a separate written guide.

If Orlando asked to review first, show the outline or script and wait. Otherwise render straight away.

## 3. Render

Use Kokoro: it reads exactly what's in the script, and accuracy matters more to him than a natural-sounding conversation.

```
cd ~/ai/tools/podcast
uv run render_kokoro.py <script.md> <project>/podcast/<slug>.mp3
```

- About 7x real time (25 minutes in about 4).
- `--limit 7` renders a quick sample of the first exchanges.
- The renderer checks the script before the voice loads. A line that isn't an `A:`/`B:` turn stops it (fix it, or `--force` drops it). It also lists each spaced acronym it joins: read the list, since a lone "A" or "I" before one is swallowed ("Plan A I liked" becomes "Plan AI liked").
- Re-render whenever the script changes; it's cheap.

VibeVoice was dropped (it invents speech). If Orlando asks for a more natural engine, start from `vibevoice-learnings.md` in the tool folder.

## 4. Hand off

Report the MP3 path, length and album, say it is tagged for Apple Music, and offer to add it there. Don't copy it to iCloud Drive or ask about iCloud: Music carries it to his phone.

A re-render doesn't update a copy already in Apple Music: tell him to delete the old track in Music, then add the new file.
````

Previous: [Publish setup](https://oneillo.com/ai-setup/skills/publish-setup/).
Next: [Heartbeat](https://oneillo.com/ai-setup/skills/heartbeat/).

[Back to Skills](https://oneillo.com/ai-setup/skills/)

[Borrowed something? Tell me how it went](https://www.linkedin.com/in/oneillo/)

Text: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Scripts: [MIT](https://opensource.org/license/mit).
