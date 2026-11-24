# Podcast

- Kind: Claude Code skill
- Address: https://oneillo.com/ai-setup/skills/podcast/
- Last updated: 2026-11-22

> AI-written and cleaned from one person's working setup. Placeholders in angle brackets are not real values: ask your user for theirs. The newest version is at https://oneillo.com/ai-setup/skills/podcast/.

Turns source documents into a two-host podcast-style MP3 by writing a script, then rendering it locally with a text-to-speech voice.

- **What it is:** Podcast is a Claude Code skill that turns source documents into a two-host audio conversation and renders it to an MP3 on a Mac.
- **How it starts:** You type `/podcast`. Claude can also pick it up when it fits.
- **Where the file goes:** `~/.claude/skills/podcast/SKILL.md`
- **Needs:** uv, Kokoro, and macOS
- **To make it yours:** Put your own values where you see `<series name>`.
  Taken out for privacy: 1 family detail, 3 career details, and 1 money detail.

## The skill file (`SKILL.md`)

The skill's instructions for turning source documents into a two-host script and rendering it to a tagged MP3 with a local voice engine, then handing it off.

````
---
name: podcast
description: Turn source documents into a two-host, podcast-style MP3 Orlando can listen to while exercising, commuting or doing chores. Writes the script, then renders it locally with Kokoro.
argument-hint: [sources (files or folder)] [length and focus, optional]
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, AskUserQuestion
---

# Podcast

Orlando does most of his "reading" as audio. This skill turns material into a two-host conversation and renders it to MP3 on his Mac. No cloud TTS, no accounts.

Tool folder (render scripts): `~/ai/tools/podcast/`.

## 1. Inputs

- **Sources:** files or a folder from the arguments or the conversation. Read them fully. If the sources conflict, go with the most recent and well-sourced one, and note the conflict to Orlando in chat (not in the audio).
- **Length:** default about 20-25 minutes. Plan on 150 spoken words per minute (25 min ≈ 3,750 words).
- **Focus:** what the listening is for (preparing for a talk, learning a topic, a book summary). Infer it from context. Ask one question only if you can't.

## 2. Write the script

Save to `<project>/podcast/<slug>-script.md`, where `<project>` is the folder the sources belong to (create `podcast/` if needed).

Format:

```
---
title: "The big picture"
album: "<series name>"
track: 1
sources: ...
pronounce: GIF=jif; SQL=sequel
---

A: First line from the lead host.
B: Co-host reply.
```

- One turn per line, starting with `A:` (lead host, knows the material) or `B:` (co-host, asks what Orlando would ask and pushes back). Only `A:`/`B:` lines are spoken.
- `pronounce:` maps words the voices may get wrong (names, brands, homographs) to spellings that sound right. Use it for anything unusual.
- `title:`, `album:` and `track:` become the MP3's tags, so Apple Music shows each set as one album by the artist Claude AI (the renderer sets the artist). Keep the title short, since the album carries the series name. Use one album per set of episodes, the same on every episode, and number the tracks in listening order. A set of take twos gets its own album, such as "..., take two".

Writing for the ear:
- Open with why this episode matters and the single most important takeaway. Close with a recap and anything Orlando can think through while he listens.
- Talk to the listener as "you." Keep turns short: 1-4 sentences. A single line of "Why?" or "Got it." is fine.
- Spell numbers the way they're said: "about eight point three percent," "a hundred twenty-four thousand," "fiscal twenty-eight." Round where precision doesn't matter.
- Write acronyms as spaced capitals when they're spelled out loud ("A P I," "C L I"); write ones said as words normally ("NASA").
- Plain sentences only: no markdown, bullets, parentheses, URLs, or em dashes (use commas or periods).
- Facts come only from the sources. Say "unverified" or "an estimate" out loud when the source does. Don't read long verbatim quotes; paraphrase.
- Signpost sections ("Okay, culture.") so the structure survives without visuals.
- Listen-only: no rehearsal pauses, silent gaps or prompts to practice out loud. He listens to remember the material; if he needs to rehearse, give him a separate written guide.

If Orlando asked to review first, show the outline or the script and wait. Otherwise go straight to rendering.

## 3. Render

Use Kokoro. Orlando prefers it: it reads exactly what's in the script, and accuracy matters more to him than a natural-sounding conversation.

```
cd ~/ai/tools/podcast
uv run render_kokoro.py <script.md> <project>/podcast/<slug>.mp3
```

- About 7x faster than real time (a 25-minute episode in about 4 minutes).
- Use `--limit 7` for a quick sample of the first few exchanges.
- Before the voice loads, the renderer checks the script. A line that isn't an `A:`/`B:` turn stops it (fix the line, or pass `--force` to drop it). It also lists each spaced acronym it joins: read the list, since a lone "A" or "I" in front of one is swallowed ("Plan A I liked" becomes "Plan AI liked").
- When the script changes, re-render; it's cheap.

VibeVoice was tried and dropped (it invents speech). If Orlando asks for a more natural-sounding engine, start from `vibevoice-learnings.md` in the tool folder.

## 4. Hand off

Report the MP3 path, length and album, say it is tagged for Apple Music, and offer to add it there. Don't copy it to iCloud Drive or ask about iCloud: Music carries it to his phone.

A re-render doesn't update a copy he has already added to Apple Music. Tell him to delete the old track in Music, then add the new file.
````

Previous: [Publish setup](https://oneillo.com/ai-setup/skills/publish-setup/).
Next: [Video title cards](https://oneillo.com/ai-setup/skills/video-title-cards/).

[Back to Skills](https://oneillo.com/ai-setup/skills/)

[Borrowed something? Tell me how it went](https://www.linkedin.com/in/oneillo/)

Text: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Scripts: [MIT](https://opensource.org/license/mit).
