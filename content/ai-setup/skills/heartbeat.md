---
title: "Heartbeat: a Claude Code skill that manages scheduled jobs on your Mac"
date: 2026-11-22
lastmod: 2026-11-27
summary: "AI-written: Adds, changes, pauses, or removes scheduled jobs on a Mac scheduler that runs a free check first, then a capped agent session if there is work."
heading: "Heartbeat"
linkTitle: "Heartbeat"
author: "Orlando O'Neill"
images: ["/ai-setup/cards/skills/heartbeat.png"]
ShowReadingTime: false
ShowShareButtons: false
disableShare: true
ShowPostNavLinks: false
ShowBreadCrumbs: true
ShowWordCount: false
hideMeta: true
madeBy: "setup-share"
---

<div class="post-description">Adds, changes, pauses, or removes scheduled jobs on a Mac scheduler that runs a free check first, then a capped agent session if there is work.</div>

<p class="ai-setup-label">Written by AI from Orlando's setup. Last updated <time datetime="2026-11-27">2026-11-27</time>. Also changed: <time datetime="2026-11-22">2026-11-22</time>. <a href="/ai-setup/#how-this-section-is-made">How this is made</a>.</p>

<section class="facts facts-skill">

<div class="facts-head"><span class="tag tag-skill">Skill</span><span class="facts-cmd"><code>/heartbeat</code></span></div>

- **What it is:** Heartbeat jobs is a Claude Code skill that adds, changes, pauses, and removes the scheduled jobs of a Mac scheduler.
- **How it starts:** You type <code>&#47;heartbeat</code>. Claude can also pick it up when it fits.
- **Where the file goes:** <code>&#126;&#47;&#46;claude&#47;skills&#47;heartbeat&#47;SKILL&#46;md</code>
- **Needs:** the heartbeat scheduler, Rex, and macOS

<div class="works">

**Works with:**
- **Calls:** <a href="/ai-setup/skills/relay/">Relay</a>.
- **Also mentions:** <a href="/ai-setup/skills/rex/">Rex</a>.

</div>

</section>

<section class="file">

## The skill file (<code>SKILL&#46;md</code>) {#skill-md}

<a href="#skill-md">Link to this file</a>

The skill's instructions for managing scheduled jobs on the heartbeat, covering how to see what jobs exist, write a new job file, and change, pause, or remove a job.

<figure class="code">
<div class="code-bar"><span class="code-name">SKILL.md</span><span class="code-meta">Markdown · 80 lines</span><button type="button" class="ai-setup-copy copy" hidden>Copy</button></div>

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere">---
name: heartbeat
description: Adds, changes, pauses or removes jobs on Orlando&#x27;s heartbeat, the scheduler on his Mac (a free check, then a capped agent session if there is work). Use when he asks for something to run on a schedule (&quot;every morning, check...&quot;, &quot;every 30 minutes, watch...&quot;), to change when a job runs, to pause or resume the heartbeat or one job, to watch another relay, or what jobs he has.
---

# Heartbeat jobs

Lives in `~/ai/tools/heartbeat/`, one file per job in `jobs/`. Program: `~/ai/tools/heartbeat/app/bin/heartbeat` (below, `heartbeat`).

**Only in a session Orlando is in.** If the heartbeat started you (instructions begin &quot;You are running unattended&quot; or &quot;You are running for a scheduler&quot;), say so and stop. A job must never change jobs.

## What is there

Start here when he asks what he has:

- `heartbeat jobs`: the jobs and anything wrong in their files.
- `heartbeat status`: running or paused, each job&#x27;s last and next run.
- `heartbeat log`: latest runs, their cost, where each kept answer is.
- `heartbeat doctor`, when he asks whether it is working: ends &quot;All is well.&quot; only when nothing needs him; otherwise names each problem (timer stopped ticking, heartbeat stuck, job paused itself, job state can&#x27;t be saved) with what to type under &quot;To fix:&quot;. Tell him those lines as they are.

## A new job

1. Ask, one question at a time, only what he hasn&#x27;t said: what it should do, when, and how to tell cheaply whether there is work (the check). Suggest a check when you can: a job without one starts an agent every time.
2. Write `jobs/<span class="ph">&lt;short-name&gt;</span>.md` (lowercase, hyphens): settings, then the agent&#x27;s checklist in plain words.

```
---
when: daily 07:00
agent: report
check: checks/inbox-has-mail.sh
tools: Read, Grep, Glob
folders: ~/ai/knowledge-base
timeout: 10m
budget: 0.50
max-runs-per-day: 2
---
# Morning inbox

1. Read what the check found.
2. Say in one short list what needs Orlando.
```

| Setting | What it takes | Left out |
|---|---|---|
| `when` | `every 30m`, `every 2h`, `daily 07:00`, `weekdays 07:00`, `weekly mon 09:00` (the Mac&#x27;s time) | required |
| `agent` | an agent&#x27;s name, or `none` when the check does the whole job | the tool&#x27;s default |
| `check` | a command run from the heartbeat&#x27;s folder: exit 0 means work, 1 means none | always work |
| `mode` | `unattended`, or `attended`: a session in a Rex pane he can watch and answer from his phone | `unattended` |
| `tools` | the tools the run may use, and the only ones it has; an attended run asks before other uses of them | reading only |
| `folders` | folders the run may work in | none |
| `timeout`, `budget` | `90s`, `10m`, `2h` (at most `24h`); dollars per run | `10m`, `0.50` |
| `max-runs-per-day` | a daily cap | `24` |
| `billing` | `plan` or `api` | `plan` |
| `enabled` | `yes` or `no` | `yes` |

   His own checks go in `checks/`, executable. They run with a cleared environment and `PATH` `/usr/bin:/bin:/usr/sbin:/sbin`, so name other programs by full path and read no key or token from the environment. Try it once from `~/ai/tools/heartbeat/` and tell him what it printed and its exit code.
3. Run `heartbeat jobs`; fix the file until the job is listed without a mistake.
4. Show him the whole file and its path, and when it first runs (`heartbeat status`). A new job waits for its first scheduled time.

**Ask first, saying what it means,** before writing:
- `billing: api`: runs are paid from his API credits instead of his plan.
- Tools that change things or reach out (`Write`, `Edit`, `Bash`, `WebFetch`, `WebSearch`), or folders wider than the job needs (like `~/ai`). Any `Bash(...)` rule counts as `Bash` however narrow it reads (`Bash(git *)` can run anything in the sandbox via a git alias or `sh -c`); say so when you ask. The heartbeat refuses `~` and any folder above it.
- Budget over `1.00`, timeout over `30m`, or daily cap over `24`.
- `mode: attended` (what he means by &quot;a session I can answer from my phone&quot;). Opens in a Rex pane with Remote Control on. No spending cap (`budget` doesn&#x27;t apply; only `timeout` and him watching limit it), and no `billing: api`: it runs on his plan. Has only the tools in `tools:`, so name `Edit` or `Write` if it should change files. Before its first run, Claude Code must trust the folder these sessions start in: `heartbeat doctor` names it and gives the command, which he runs once at the Mac&#x27;s keyboard (not from his phone). Until then the job runs unattended.

Never add anything that turns permission checks off. The heartbeat refuses it, and so should you.

## Change, pause, remove

- **When it runs:** change `when:`. Applies at the next tick (within 30 minutes).
- **Pause everything:** `heartbeat pause`; `heartbeat resume` restarts. Jobs that were due catch up once.
- **Pause one job:** `enabled: no` in its file; `enabled: yes` turns it back on.
- **A job that paused itself** after three failures in a row (a working run ends the row, and so does a check finding no work, unless a run in the row failed): say why (`heartbeat log --job <span class="ph">&lt;name&gt;</span>`), then `heartbeat resume <span class="ph">&lt;name&gt;</span>` once he agrees.
- **Remove a job:** with his yes, `trash` its file (and its check, if nothing else uses it).
- **Watch another relay:** add the relay&#x27;s folder at the end of the `check:` line of `jobs/relay-watchdog.md`. `app/checks/relay-stalled.sh --dry-run <span class="ph">&lt;folder&gt;</span>`, run from the heartbeat&#x27;s folder, shows what the watchdog sees there. When a relay is finished and its folder moved, take the folder off the check line (the watchdog says once that it has no roadmap.md and keeps watching the others).
- **Stop a watched relay:** in its roadmap, write the template&#x27;s form `- Relay: **off**`. The watchdog starts a lead only when the Controls say exactly `- Relay: **on**`, but the relay skill reads other forms differently. Closing the lead&#x27;s pane isn&#x27;t enough: the watchdog restarts a closed pane.
- **Watchdog says a roadmap can&#x27;t be read:** nothing starts in that relay until the named line is fixed, in the template&#x27;s form. It says so once.
- **Watchdog says a lead has sat idle:** it may have stopped at a usage limit mid-turn, and no other lead can start while it is there. He can tell it to carry on in the Claude app, or close its pane; the watchdog then starts a fresh lead.

After any change, run `heartbeat jobs` and show him the lines you changed.
</code></pre>

</figure>

</section>

<nav class="page-foot">

Previous: <a href="/ai-setup/skills/podcast/">Podcast</a>.
Next: <a href="/ai-setup/skills/relay/">Relay</a>.

<a href="/ai-setup/skills/">Back to Skills</a>

<a href="https://www.linkedin.com/in/oneillo/" target="_blank" rel="noopener">Borrowed something? Tell me how it went</a>

Text: <a href="https://creativecommons.org/licenses/by/4.0/" target="_blank" rel="license noopener">CC BY 4.0</a>. Scripts: <a href="https://opensource.org/license/mit" target="_blank" rel="license noopener">MIT</a>.

</nav>
