---
title: "Podcast: a Claude Code skill that turns documents into a two-host podcast"
date: 2026-11-22
lastmod: 2026-11-22
summary: "AI-written: Turns source documents into a two-host podcast-style MP3 by writing a script, then rendering it locally with a text-to-speech voice."
heading: "Podcast"
linkTitle: "Podcast"
author: "Orlando O'Neill"
images: ["/ai-setup/cards/skills/podcast.png"]
ShowReadingTime: false
ShowShareButtons: false
disableShare: true
ShowPostNavLinks: false
ShowBreadCrumbs: true
ShowWordCount: false
hideMeta: true
madeBy: "setup-share"
---

<div class="post-description">Turns source documents into a two-host podcast-style MP3 by writing a script, then rendering it locally with a text-to-speech voice.</div>

> **Written by AI.** This page was generated from Orlando's own setup by an automated run, and checked for private details before it was published. <a href="/ai-setup/#how-this-section-is-made">How this section is made</a>. Last updated 2026-11-22.

- **What it is:** Podcast is a Claude Code skill that turns source documents into a two-host audio conversation and renders it to an MP3 on a Mac.
- **How it starts:** You type <code>&#47;podcast</code>. Claude can also pick it up when it fits.
- **Where the file goes:** <code>&#126;&#47;&#46;claude&#47;skills&#47;podcast&#47;SKILL&#46;md</code>
- **Needs:** uv, Kokoro, and macOS
- **To make it yours:** Put your own values where you see <code>&#60;series name&#62;</code>.
  Taken out for privacy: 1 family detail, 3 career details, and 1 money detail.

## The skill file (<code>SKILL&#46;md</code>) {#skill-md}

<a href="#skill-md">Link to this file</a>

The skill's instructions for turning source documents into a two-host script and rendering it to a tagged MP3 with a local voice engine, then handing it off.

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere">---
name: podcast
description: Turn source documents into a two-host, podcast-style MP3 Orlando can listen to while exercising, commuting or doing chores. Writes the script, then renders it locally with Kokoro.
argument-hint: [sources (files or folder)] [length and focus, optional]
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, AskUserQuestion
---

# Podcast

Orlando does most of his &quot;reading&quot; as audio. This skill turns material into a two-host conversation and renders it to MP3 on his Mac. No cloud TTS, no accounts.

Tool folder (render scripts): `~/ai/tools/podcast/`.

## 1. Inputs

- **Sources:** files or a folder from the arguments or the conversation. Read them fully. If the sources conflict, go with the most recent and well-sourced one, and note the conflict to Orlando in chat (not in the audio).
- **Length:** default about 20-25 minutes. Plan on 150 spoken words per minute (25 min ≈ 3,750 words).
- **Focus:** what the listening is for (preparing for a talk, learning a topic, a book summary). Infer it from context. Ask one question only if you can&#x27;t.

## 2. Write the script

Save to `&lt;project&gt;/podcast/&lt;slug&gt;-script.md`, where `&lt;project&gt;` is the folder the sources belong to (create `podcast/` if needed).

Format:

```
---
title: &quot;The big picture&quot;
album: &quot;&lt;series name&gt;&quot;
track: 1
sources: ...
pronounce: GIF=jif; SQL=sequel
---

A: First line from the lead host.
B: Co-host reply.
```

- One turn per line, starting with `A:` (lead host, knows the material) or `B:` (co-host, asks what Orlando would ask and pushes back). Only `A:`/`B:` lines are spoken.
- `pronounce:` maps words the voices may get wrong (names, brands, homographs) to spellings that sound right. Use it for anything unusual.
- `title:`, `album:` and `track:` become the MP3&#x27;s tags, so Apple Music shows each set as one album by the artist Claude AI (the renderer sets the artist). Keep the title short, since the album carries the series name. Use one album per set of episodes, the same on every episode, and number the tracks in listening order. A set of take twos gets its own album, such as &quot;..., take two&quot;.

Writing for the ear:
- Open with why this episode matters and the single most important takeaway. Close with a recap and anything Orlando can think through while he listens.
- Talk to the listener as &quot;you.&quot; Keep turns short: 1-4 sentences. A single line of &quot;Why?&quot; or &quot;Got it.&quot; is fine.
- Spell numbers the way they&#x27;re said: &quot;about eight point three percent,&quot; &quot;a hundred twenty-four thousand,&quot; &quot;fiscal twenty-eight.&quot; Round where precision doesn&#x27;t matter.
- Write acronyms as spaced capitals when they&#x27;re spelled out loud (&quot;A P I,&quot; &quot;C L I&quot;); write ones said as words normally (&quot;NASA&quot;).
- Plain sentences only: no markdown, bullets, parentheses, URLs, or em dashes (use commas or periods).
- Facts come only from the sources. Say &quot;unverified&quot; or &quot;an estimate&quot; out loud when the source does. Don&#x27;t read long verbatim quotes; paraphrase.
- Signpost sections (&quot;Okay, culture.&quot;) so the structure survives without visuals.
- Listen-only: no rehearsal pauses, silent gaps or prompts to practice out loud. He listens to remember the material; if he needs to rehearse, give him a separate written guide.

If Orlando asked to review first, show the outline or the script and wait. Otherwise go straight to rendering.

## 3. Render

Use Kokoro. Orlando prefers it: it reads exactly what&#x27;s in the script, and accuracy matters more to him than a natural-sounding conversation.

```
cd ~/ai/tools/podcast
uv run render_kokoro.py &lt;script.md&gt; &lt;project&gt;/podcast/&lt;slug&gt;.mp3
```

- About 7x faster than real time (a 25-minute episode in about 4 minutes).
- Use `--limit 7` for a quick sample of the first few exchanges.
- Before the voice loads, the renderer checks the script. A line that isn&#x27;t an `A:`/`B:` turn stops it (fix the line, or pass `--force` to drop it). It also lists each spaced acronym it joins: read the list, since a lone &quot;A&quot; or &quot;I&quot; in front of one is swallowed (&quot;Plan A I liked&quot; becomes &quot;Plan AI liked&quot;).
- When the script changes, re-render; it&#x27;s cheap.

VibeVoice was tried and dropped (it invents speech). If Orlando asks for a more natural-sounding engine, start from `vibevoice-learnings.md` in the tool folder.

## 4. Hand off

Report the MP3 path, length and album, say it is tagged for Apple Music, and offer to add it there. Don&#x27;t copy it to iCloud Drive or ask about iCloud: Music carries it to his phone.

A re-render doesn&#x27;t update a copy he has already added to Apple Music. Tell him to delete the old track in Music, then add the new file.
</code></pre>
