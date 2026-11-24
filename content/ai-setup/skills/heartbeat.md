---
title: "Heartbeat"
date: 2026-11-22
lastmod: 2026-11-22
description: "AI-written: This skill lets an agent add, change, pause, or remove jobs on a scheduler that runs a free check on a timer and starts a limited agent session only when there is work."
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

Each job is one small file with settings and a checklist, and the skill sets out what to ask first and how to check the result.

**Works with:** <a href="/ai-setup/skills/relay/">Relay</a> and <a href="/ai-setup/skills/rex/">Rex</a>.

## <code>SKILL&#46;md</code>

The instructions an agent follows when its owner asks to add, change, pause, or remove a scheduled job, including the job file format and what to confirm first.

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere">---
name: heartbeat
description: Add, change, pause or remove a job on Orlando&#x27;s heartbeat, the scheduler on his Mac that runs a free check and then, if there is work, an agent session with limits. Use when he asks in plain words for something to run on a schedule (&quot;every morning, check...&quot;, &quot;every 30 minutes, watch...&quot;), to change when a job runs, to pause or resume the heartbeat or one job, to watch another relay, or what jobs he has.
---

# Heartbeat jobs

The heartbeat lives in `~/ai/tools/heartbeat/`. Each job is one file in its `jobs/` folder. The program is `~/ai/tools/heartbeat/app/bin/heartbeat` (below, `heartbeat`).

**Only in a session Orlando is in.** If the heartbeat started you (your instructions begin &quot;You are running unattended&quot; or &quot;You are running for a scheduler&quot;), don&#x27;t use this skill: say so and stop. A job must never change jobs.

## What is there

`heartbeat jobs` lists the jobs and anything wrong in their files. `heartbeat status` says whether it is running or paused, and each job&#x27;s last and next run. `heartbeat log` shows the latest runs, what they cost, and where each kept answer is. Start here when he asks what he has. When he asks whether it is working, run `heartbeat doctor`: it ends &quot;All is well.&quot; only when nothing needs him, and otherwise names each problem (a timer that has stopped ticking, a heartbeat that is stuck, a job that paused itself, a job whose state can&#x27;t be saved) with what to type under &quot;To fix:&quot;. Tell him those lines as they are.

## A new job

1. Get what you need, one question at a time, and only what he hasn&#x27;t said: what it should do, when, and how to tell cheaply whether there is any work (the check). Suggest a check yourself when you can: a job without one starts an agent every time.
2. Write `jobs/&lt;short-name&gt;.md` (lowercase, hyphens). Settings, then the checklist the agent follows, in plain words:

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

   A check of his own goes in `checks/`, made executable. The heartbeat starts it with a cleared environment and a short fixed `PATH`, so it names other programs by their full path and reads no key or token from the environment. Try it once from `~/ai/tools/heartbeat/` and tell him what it printed and its exit code.
3. Run `heartbeat jobs`. If it names a mistake, fix the file and run it again until the job is listed without one.
4. Show him the whole file and its path, and say when it first runs (`heartbeat status`). A new job waits for its first scheduled time.

**Ask first, and say what it means,** before writing any of these:
- `billing: api`: runs are paid from his API credits instead of his plan.
- Tools that change things or reach out (`Write`, `Edit`, `Bash`, `WebFetch`, `WebSearch`), or folders wider than the job needs (like `~/ai`). Any `Bash(...)` rule counts as `Bash`, however narrow it reads: `Bash(git *)` lets a command run anything inside the sandbox, through a git alias or `sh -c`. Say so when you ask. The heartbeat refuses `~` and any folder above it.
- A budget over `1.00`, a timeout over `30m`, or a daily cap over `24`.
- `mode: attended` (what he means by &quot;a session I can answer from my phone&quot;). It opens in a Rex pane with Remote Control on. It has no spending cap (`budget` doesn&#x27;t apply; only `timeout` and him watching limit it), and it can&#x27;t use `billing: api`: it runs on his plan. It has only the tools in `tools:`, so if it should change files, name `Edit` or `Write`. Before its first run, Claude Code must trust the folder these sessions start in: `heartbeat doctor` names it and gives the command, which he runs once at the Mac&#x27;s keyboard (not from his phone). Until then, the job runs unattended.

Never add anything that turns permission checks off. The heartbeat refuses it, and so should you.

## Change, pause, remove

- **When it runs:** change the `when:` line. It applies at the next tick (within 30 minutes).
- **Pause everything:** `heartbeat pause`, and `heartbeat resume` to start again. Jobs that were due catch up once.
- **Pause one job:** `enabled: no` in its file, `enabled: yes` to switch it back on.
- **A job that paused itself** after three failures in a row (a run that works ends the row, and so does a check that finds no work, unless a run in the row failed): say why (`heartbeat log --job &lt;name&gt;`), then `heartbeat resume &lt;name&gt;` once he agrees.
- **Remove a job:** with his yes, `trash` its file (and its check, if nothing else uses it).
- **Watch another relay:** add the relay&#x27;s folder at the end of the `check:` line of `jobs/relay-watchdog.md`. `app/checks/relay-stalled.sh --dry-run &lt;folder&gt;`, run from the heartbeat&#x27;s folder, says what the watchdog sees there. When a relay is finished and its folder moved, take the folder off the check line: the watchdog says once that it has no roadmap.md, and goes on watching the others.
- **Stop a watched relay:** set Relay to off in its roadmap. Write it in the template&#x27;s form, `- Relay: **off**`: the watchdog starts a lead only when the Controls say exactly `- Relay: **on**`, but the relay skill itself reads other forms differently. Closing its lead&#x27;s pane is not enough: the watchdog restarts a closed pane.
- **The watchdog says a roadmap can&#x27;t be read:** nothing starts in that relay until the line it names is fixed, in the template&#x27;s form. It says so once.
- **The watchdog says a lead has sat idle:** it may have stopped at a usage limit in the middle of a turn, and no other lead can start while it is there. He can tell it to carry on in the Claude app, or close its pane; the watchdog then starts a fresh lead.

After any change, run `heartbeat jobs` and show him the lines you changed.
</code></pre>

Taken out: 1 security detail (the exact path list given to checks).
