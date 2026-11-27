---
title: "Podcast: a Claude Code skill that turns documents into a two-host podcast"
date: 2026-11-22
lastmod: 2026-11-27
summary: "AI-written: Turns source documents into a two-host podcast MP3, writing the script and rendering the voices on the Mac."
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

<div class="post-description">Turns source documents into a two-host podcast MP3, writing the script and rendering the voices on the Mac.</div>

<p class="ai-setup-label">Written by AI from Orlando's setup. Last updated <time datetime="2026-11-27">2026-11-27</time>. Also changed: <time datetime="2026-11-22">2026-11-22</time>. <a href="/ai-setup/#how-this-section-is-made">How this is made</a>.</p>

<section class="facts facts-skill">

<div class="facts-head"><span class="tag tag-skill">Skill</span><span class="facts-cmd"><code>/podcast</code></span></div>

- **What it is:** Podcast is a Claude Code skill that turns source documents into a two-host MP3 episode, with the script written and the voices rendered locally.
- **How it starts:** You type <code>&#47;podcast</code>. Claude can also pick it up when it fits.
- **Where the file goes:** <code>&#126;&#47;&#46;claude&#47;skills&#47;podcast&#47;SKILL&#46;md</code>
- **Needs:** Kokoro, uv, and Apple Music
- **To make it yours:** Put your own values where you see <code>&#60;album&#45;name&#62;</code>.
  Taken out for privacy: 1 family detail and 1 career detail.

</section>

<section class="file">

## The skill file (<code>SKILL&#46;md</code>) {#skill-md}

<a href="#skill-md">Link to this file</a>

The main instructions for the podcast skill: how to read the sources, write a two-host script, render it to an MP3 with Kokoro, and hand it off.

<figure class="code">
<div class="code-bar"><span class="code-name">SKILL.md</span><span class="code-meta">Markdown · 71 lines</span><button type="button" class="ai-setup-copy copy" hidden>Copy</button></div>

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere">---
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
- **Focus:** what it&#x27;s for (learning a topic, a book summary). Infer it; ask one question only if you can&#x27;t.

## 2. Write the script

Save to `<span class="ph">&lt;project&gt;</span>/podcast/<span class="ph">&lt;slug&gt;</span>-script.md`, `<span class="ph">&lt;project&gt;</span>` being the folder the sources belong to (create `podcast/` if needed).

```
---
title: &quot;The big picture&quot;
album: &quot;<span class="ph">&lt;album-name&gt;</span>&quot;
track: 1
sources: ...
pronounce: GIF=jif; SQL=sequel
---

A: First line from the lead host.
B: Co-host reply.
```

- One turn per line: `A:` (lead host, knows the material) or `B:` (co-host, asks what Orlando would ask and pushes back). Only `A:`/`B:` lines are spoken.
- `pronounce:` maps words the voices may get wrong (names, brands, homographs, anything unusual) to spellings that sound right.
- `title:`, `album:`, `track:` become MP3 tags, so Apple Music shows each set as one album by the artist Claude AI (the renderer sets the artist). Short title, since the album carries the series name. One album per set, the same on every episode, tracks numbered in listening order. A set of take twos gets its own album (&quot;..., take two&quot;).

Writing for the ear:
- Open with why the episode matters and the single most important takeaway. Close with a recap and anything Orlando can think through while he listens.
- Address the listener as &quot;you.&quot; Turns of 1-4 sentences; a lone &quot;Why?&quot; or &quot;Got it.&quot; is fine.
- Numbers as said: &quot;about eight point three billion dollars,&quot; &quot;a hundred twenty-four thousand,&quot; &quot;fiscal twenty-eight.&quot; Round where precision doesn&#x27;t matter.
- Acronyms spelled out loud as spaced capitals (&quot;G P M,&quot; &quot;O K Rs&quot;); ones said as words written normally (&quot;NASA&quot;).
- Plain sentences only: no markdown, bullets, parentheses, URLs or em dashes.
- Facts only from the sources. Say &quot;unverified&quot; or &quot;an estimate&quot; out loud when the source does. Paraphrase rather than read long quotes.
- Signpost sections (&quot;Okay, culture.&quot;) so structure survives without visuals.
- Listen-only: no rehearsal pauses, silent gaps or prompts to practice out loud: he listens to remember the material. If he needs to rehearse, give him a separate written guide.

If Orlando asked to review first, show the outline or script and wait. Otherwise render straight away.

## 3. Render

Use Kokoro: it reads exactly what&#x27;s in the script, and accuracy matters more to him than a natural-sounding conversation.

```
cd ~/ai/tools/podcast
uv run render_kokoro.py <span class="ph">&lt;script.md&gt;</span> <span class="ph">&lt;project&gt;</span>/podcast/<span class="ph">&lt;slug&gt;</span>.mp3
```

- About 7x real time (25 minutes in about 4).
- `--limit 7` renders a quick sample of the first exchanges.
- The renderer checks the script before the voice loads. A line that isn&#x27;t an `A:`/`B:` turn stops it (fix it, or `--force` drops it). It also lists each spaced acronym it joins: read the list, since a lone &quot;A&quot; or &quot;I&quot; before one is swallowed (&quot;Plan A I liked&quot; becomes &quot;Plan AI liked&quot;).
- Re-render whenever the script changes; it&#x27;s cheap.

VibeVoice was dropped (it invents speech). If Orlando asks for a more natural engine, start from `vibevoice-learnings.md` in the tool folder.

## 4. Hand off

Report the MP3 path, length and album, say it is tagged for Apple Music, and offer to add it there. Don&#x27;t copy it to iCloud Drive or ask about iCloud: Music carries it to his phone.

A re-render doesn&#x27;t update a copy already in Apple Music: tell him to delete the old track in Music, then add the new file.
</code></pre>

</figure>

</section>

<nav class="page-foot">

Previous: <a href="/ai-setup/skills/publish-setup/">Publish setup</a>.
Next: <a href="/ai-setup/skills/heartbeat/">Heartbeat</a>.

<a href="/ai-setup/skills/">Back to Skills</a>

<a href="https://www.linkedin.com/in/oneillo/" target="_blank" rel="noopener">Borrowed something? Tell me how it went</a>

Text: <a href="https://creativecommons.org/licenses/by/4.0/" target="_blank" rel="license noopener">CC BY 4.0</a>. Scripts: <a href="https://opensource.org/license/mit" target="_blank" rel="license noopener">MIT</a>.

</nav>
