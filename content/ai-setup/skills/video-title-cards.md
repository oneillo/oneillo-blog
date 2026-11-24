---
title: "Video title cards: a Claude Code skill that adds intro and outro title cards to videos"
date: 2026-11-22
lastmod: 2026-11-22
summary: "AI-written: Makes intro, outro, and interstitial title cards with ffmpeg and joins them to screen recordings."
heading: "Video title cards"
linkTitle: "Video title cards"
author: "Orlando O'Neill"
images: ["/ai-setup/cards/skills/video-title-cards.png"]
ShowReadingTime: false
ShowShareButtons: false
disableShare: true
ShowPostNavLinks: false
ShowBreadCrumbs: true
ShowWordCount: false
hideMeta: true
madeBy: "setup-share"
---

<div class="post-description">Makes intro, outro, and interstitial title cards with ffmpeg and joins them to screen recordings.</div>

> **Written by AI.** This page was generated from Orlando's own setup by an automated run, and checked for private details before it was published. <a href="/ai-setup/#how-this-section-is-made">How this section is made</a>. Last updated 2026-11-22.

- **What it is:** Video title cards is a Claude Code skill that makes intro, outro, and interstitial title cards with ffmpeg and joins them to a screen recording.
- **How it starts:** You type <code>&#47;video&#45;title&#45;cards</code>. Claude can also pick it up when it fits.
- **Where the file goes:** <code>&#126;&#47;&#46;claude&#47;skills&#47;video&#45;title&#45;cards&#47;SKILL&#46;md</code>
- **Needs:** ffmpeg-full, Homebrew, uv, and mlx-whisper

## The skill file (<code>SKILL&#46;md</code>) {#skill-md}

<a href="#skill-md">Link to this file</a>

The full instructions for making title cards with ffmpeg and joining them to a screen recording, used when someone asks for intro, end, or recap cards on a video.

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere">---
name: video-title-cards
description: Generate intro and outro title cards for videos using ffmpeg, and prepend/append them to screen recordings. Use when the user asks to add title cards, intro cards, end cards, or recap cards to a video.
allowed-tools: Read, Write, Bash, Glob, Grep, AskUserQuestion
---

# Video Title Cards

Generate title cards with text using ffmpeg and concatenate them with screen recordings.

## Prerequisites

- `ffmpeg-full` must be installed (`brew install ffmpeg-full`). Call it by its path, `/opt/homebrew/opt/ffmpeg-full/bin/ffmpeg`, as every command below does: the `ffmpeg` on the path is the plain build, which lacks the `drawtext` filter (no freetype). Leave that link as it is.
- `mlx-whisper` for transcription (runs via `uv run --with mlx-whisper`)

## Work Folder

Make one work folder at the start, `mkdir -p ~/ai/tmp/title-cards-&lt;slug&gt;`, and put every file that is not the final video there: audio, frames, text files, cards, cuts and previews. `WORK` in the commands below stands for its full path (write it out: the shell does not expand `~` inside a filter string). When the final video is built and checked, remove the folder: `rm -rf ~/ai/tmp/title-cards-&lt;slug&gt;`.

## Step 0: Understand the Recording

Before writing copy, understand the video&#x27;s content. Choose the approach based on whether the video has voiceover.

### Option A: Transcribe (videos with voiceover)

1. Extract audio as 16kHz mono WAV:
   ```bash
   /opt/homebrew/opt/ffmpeg-full/bin/ffmpeg -y -i INPUT.mov -vn -acodec pcm_s16le -ar 16000 -ac 1 WORK/audio.wav
   ```

2. Transcribe with mlx-whisper (runs locally on Apple Silicon, no network required):
   ```python
   uv run --with mlx-whisper python3 -c &quot;
   import mlx_whisper
   result = mlx_whisper.transcribe(&#x27;WORK/audio.wav&#x27;, path_or_hf_repo=&#x27;mlx-community/whisper-small-mlx&#x27;)
   print(result[&#x27;text&#x27;])
   &quot;
   ```

3. Use the transcript to identify the video&#x27;s topic, key points, and messaging.

### Option B: Frame extraction (silent videos / screen recordings without voiceover)

When the video has no voiceover, extract frames and read them visually to understand the content.

1. Extract frames at 15-second intervals for an initial overview:
   ```bash
   mkdir -p WORK/video-frames
   /opt/homebrew/opt/ffmpeg-full/bin/ffmpeg -y -i INPUT.mov -vf &quot;fps=1/15&quot; -q:v 2 WORK/video-frames/frame_%03d.jpg
   ```

2. Read the frames as images to map the video&#x27;s flow -- identify prompts, responses, key moments, and transitions.

3. For precise cut points (editing), re-extract at 5-second intervals:
   ```bash
   mkdir -p WORK/video-frames-fine
   /opt/homebrew/opt/ffmpeg-full/bin/ffmpeg -y -i INPUT.mov -vf &quot;fps=1/5&quot; -q:v 2 WORK/video-frames-fine/frame_%03d.jpg
   ```

4. Each frame number x interval = timestamp in seconds (e.g., frame 8 at 5s intervals = 40s).

5. Use the visual content to identify the video&#x27;s topic, key points, and arc.

## Copy Workflow

Write title card copy directly. Follow this process:

1. Understand the video content (Step 0 above -- transcribe or extract frames) and extract the key points and message.
2. Draft 5 options for the intro card title and subtitle. Present them to the user.
3. Iterate if needed based on user feedback.
4. Once the user picks, do the same for the outro takeaway copy.
5. Generate the cards.

This applies to both intro card subtitles and outro card takeaway copy.

## Text on Cards

ffmpeg&#x27;s `drawtext` breaks on ordinary titles given as `text=&#x27;...&#x27;`: an apostrophe drops the line with no error, a colon or a percent sign stops the command. Put every line of text in its own file in the work folder and pass it with `textfile`, always with `expansion=none` (without it a percent sign still fails and backslashes vanish):

```bash
printf &#x27;%s&#x27; &quot;It&#x27;s 100% local: no cloud&quot; &gt; WORK/title.txt
# Then in ffmpeg:
drawtext=textfile=WORK/title.txt:expansion=none:fontsize=62:...
```

Never use `text=&#x27;...&#x27;`, even for plain words.

## Step 1: Probe the Recording

Before generating title cards, probe the screen recording to get its resolution, frame rate, and audio sample rate:

```bash
ffprobe -v error -select_streams v:0 -show_entries stream=width,height,r_frame_rate INPUT.mov
ffprobe -v error -select_streams a:0 -show_entries stream=sample_rate,channels INPUT.mov
```

Use these values for the title card generation. QuickTime on macOS often records at non-standard resolutions (e.g., 2976x1922) and high frame rates (e.g., 240fps). The title cards must match.

## Step 2: Generate Title Cards

Use the resolution and audio sample rate from the probe. Scale font sizes proportionally if the resolution differs from 1920x1080 (multiply by `width/1920`).

### Manual generation (for custom layouts)

#### Intro Card

```bash
/opt/homebrew/opt/ffmpeg-full/bin/ffmpeg -f lavfi -i &quot;color=c=0xF5F5F5:s=WIDTHxHEIGHT:d=5:r=FPS&quot; \
  -f lavfi -i anullsrc=r=SAMPLE_RATE:cl=stereo \
  -vf &quot;drawtext=textfile=WORK/title.txt:expansion=none:fontsize=TITLE_SIZE:fontcolor=0x1E1E1E:x=(w-text_w)/2:y=(h-text_h)/2-40:font=Helvetica, \
       drawtext=textfile=WORK/description.txt:expansion=none:fontsize=DESC_SIZE:fontcolor=0x6C6C6C:x=(w-text_w)/2:y=(h-text_h)/2+40:font=Helvetica&quot; \
  -c:v libx264 -pix_fmt yuv420p -c:a aac -shortest intro.mov
```

#### Outro Card

The outro uses left-aligned layout with bulleted takeaways.

```bash
/opt/homebrew/opt/ffmpeg-full/bin/ffmpeg -f lavfi -i &quot;color=c=0xF5F5F5:s=WIDTHxHEIGHT:d=8:r=FPS&quot; \
  -f lavfi -i anullsrc=r=SAMPLE_RATE:cl=stereo \
  -vf &quot;drawtext=textfile=WORK/header.txt:expansion=none:fontsize=HEADER_SIZE:fontcolor=0x1E1E1E:x=LEFT_MARGIN:y=Y_HEADER:font=Helvetica, \
       drawtext=textfile=WORK/point1.txt:expansion=none:fontsize=POINT_SIZE:fontcolor=0x6C6C6C:x=LEFT_MARGIN:y=Y_POINT1:font=Helvetica, \
       drawtext=textfile=WORK/cmd.txt:expansion=none:fontsize=POINT_SIZE:fontcolor=0x1E1E1E:x=LEFT_MARGIN:y=Y_CMD:font=Helvetica, \
       drawtext=textfile=WORK/desc.txt:expansion=none:fontsize=POINT_SIZE:fontcolor=0x6C6C6C:x=LEFT_MARGIN+48:y=Y_DESC:font=Helvetica&quot; \
  -c:v libx264 -pix_fmt yuv420p -c:a aac -shortest outro.mov
```

- &quot;Takeaways&quot; header: left-aligned at same x as bullets
- Regular takeaway lines: bullet prefix, gray text (`#6C6C6C`)
- Command lines: bullet prefix, dark text (`#1E1E1E`)
- Command descriptions: indented past the bullet (x + 48 at 2976 width), gray text, same font size

Always include a silent audio track (`anullsrc`) on title cards -- without it, concatenation will fail or produce audio glitches.

## Step 2.5: Verify Title Cards

Before concatenating, extract a frame from each card and visually check margins, text placement, and readability:

```bash
/opt/homebrew/opt/ffmpeg-full/bin/ffmpeg -y -i intro.mov -vframes 1 -q:v 2 WORK/intro-preview.jpg
/opt/homebrew/opt/ffmpeg-full/bin/ffmpeg -y -i outro.mov -vframes 1 -q:v 2 WORK/outro-preview.jpg
```

Read the preview images and confirm:
- Text has comfortable margins on all sides (not edge-to-edge)
- Font sizes are readable
- Bullet points are evenly spaced
- No text is clipped or overlapping

Fix and regenerate before proceeding to concatenation.

## Step 3: Concatenate

Use `filter_complex` concat -- not the concat demuxer (`-f concat`). The demuxer causes audio drift when combining clips with different encoding parameters, even if resolution and sample rate match. The filter_complex approach normalizes everything in one pass.

Normalize all inputs to 30fps before concatenating. QuickTime&#x27;s high frame rates (120/240fps) cause timestamp issues with the concat demuxer.

```bash
/opt/homebrew/opt/ffmpeg-full/bin/ffmpeg -y \
  -i intro.mov \
  -i content.mov \
  -i outro.mov \
  -filter_complex &quot;\
    [0:v]scale=WIDTH:HEIGHT,fps=30[v0]; \
    [1:v]scale=WIDTH:HEIGHT,fps=30[v1]; \
    [2:v]scale=WIDTH:HEIGHT,fps=30[v2]; \
    [0:a]aresample=SAMPLE_RATE[a0]; \
    [1:a]aresample=SAMPLE_RATE[a1]; \
    [2:a]aresample=SAMPLE_RATE[a2]; \
    [v0][a0][v1][a1][v2][a2]concat=n=3:v=1:a=1[vout][aout]&quot; \
  -map &quot;[vout]&quot; -map &quot;[aout]&quot; \
  -c:v libx264 -pix_fmt yuv420p -c:a aac \
  output.mov
```

## Audio Cleanup

Apply these filters to the screen recording before concatenation:

```bash
/opt/homebrew/opt/ffmpeg-full/bin/ffmpeg -i recording.mov -af &quot;volume=enable=&#x27;lt(t,SECONDS)&#x27;:volume=0,highpass=f=100&quot; -c:v copy output.mov
```

- `volume=enable=&#x27;lt(t,SECONDS)&#x27;:volume=0` -- mutes the first N seconds (useful for cutting mic breath at the start)
- `highpass=f=100` -- removes low-frequency plosive pops (p/b sounds). Don&#x27;t go above 120Hz or voice sounds thin.

## Interstitial Cards

For videos with multiple segments (especially silent screen recordings), generate short interstitial cards between cuts to guide the viewer through the narrative.

```bash
/opt/homebrew/opt/ffmpeg-full/bin/ffmpeg -f lavfi -i &quot;color=c=0xF5F5F5:s=WIDTHxHEIGHT:d=3:r=FPS&quot; \
  -f lavfi -i anullsrc=r=SAMPLE_RATE:cl=stereo \
  -vf &quot;drawtext=textfile=WORK/interstitial.txt:expansion=none:fontsize=INTER_SIZE:fontcolor=0x1E1E1E:x=(w-text_w)/2:y=(h-text_h)/2:font=Helvetica&quot; \
  -c:v libx264 -pix_fmt yuv420p -c:a aac -shortest interstitial.mov
```

- Duration: 3 seconds (shorter than intro/outro -- just enough to read)
- Text: centered, dark, slightly smaller than intro title (~60pt at 1920 base)
- Keep copy short -- one line that tells the viewer what&#x27;s about to happen

## Video Editing (Cut and Assemble)

When the source recording needs editing (removing errors, dead time, or unwanted sections), cut segments and reassemble with title cards.

### Cutting segments

Strip original audio and add a silent track to each cut so all segments have matching audio streams for concatenation:

```bash
/opt/homebrew/opt/ffmpeg-full/bin/ffmpeg -y -ss START -t DURATION -i SOURCE.mov \
  -f lavfi -i &quot;anullsrc=r=SAMPLE_RATE:cl=stereo&quot; \
  -map 0:v -map 1:a \
  -c:v libx264 -pix_fmt yuv420p -c:a aac -shortest \
  segment.mov
```

Critical: Do NOT use `-an` (no audio) on segments -- the concat filter requires all inputs to have both video and audio streams. Use `anullsrc` to generate silent audio instead.

### Assembling

Use the same `filter_complex` concat approach as Step 3, with all segments (title cards, video cuts, interstitials) as inputs. Each input gets `scale=W:H,fps=30` for video and `aresample=SR` for audio.

### Verification

After building, extract frames at 10-second intervals and visually verify:
- No error messages or unwanted content visible
- Title cards render correctly
- Cuts transition at the right moments
- Final segment shows the intended closing content

## Defaults

- Background color: `#F5F5F5` (off-white, matches blog)
- Title text color: `#1E1E1E` (near-black)
- Body text color: `#6C6C6C` (medium gray)
- Intro duration: 5 seconds
- Outro duration: 8 seconds (more text to read)
- Font: Helvetica
- At 1920x1080: title 100pt, description 50pt, outro header 80pt, outro points 44pt
- Title-to-subtitle vertical gap: 160px (+/-80px from center)
- Output fps: 30 (normalized during concat regardless of source fps)

## Customization

The user may request different colors, durations, or fonts. Adjust the ffmpeg parameters accordingly.

### Outro bullet formatting
- Prefix each takeaway line with a bullet and two spaces
- Left-align bullets at x=400 (at 2976 width) or scale proportionally
- Line spacing: 130px between bullet lines (at 2976 height)
- If takeaway content exceeds what fits on one card, split across multiple outro cards of equal duration

### Line wrapping
- Keep each bullet on a single line by default. Only wrap to a second line when the text physically exceeds the available width (card width minus left margin minus a small right margin).
- Do not pre-wrap lines for aesthetics or conservative spacing -- use the full available width.
</code></pre>
