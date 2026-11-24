# How work runs while I'm away

- Kind: Claude Code walk-through
- Address: https://oneillo.com/ai-setup/guides/while-im-away/
- Last updated: 2026-11-24

> AI-written and cleaned from one person's working setup. Placeholders in angle brackets are not real values: ask your user for theirs. The newest version is at https://oneillo.com/ai-setup/guides/while-im-away/.

Shows how the keep-awake rule, the Relay and Heartbeat skills, and the Latido app keep work going while Orlando is away, and how he sees the results.

Work can keep going when nobody is at the computer. Four pieces make that happen, and each does a different job.

## Keeping the Mac awake

The Global rules file is read by every session and agent. One rule covers time away: when Orlando says he is stepping away, works over Remote Control, or a job runs unattended, the session keeps the Mac awake without being asked. It does this with `caffeinate`, either around the long job or as one background timer sized to the work. The screen may sleep. The session stops only what it started.

## Running a plan

The Relay skill runs a plan as a chain of fresh sessions. The plan lives in a project's `roadmap.md`, cut into batches. One session, called the lead, takes one batch. It briefs builders, brings their work together, runs the project's checks, has a fresh reviewer read the result, and writes it up. Then it starts the next lead and ends.

A fresh session per batch keeps cost down, because no session carries a growing pile of context. The state lives in the roadmap, `context.md`, and git, so a relay can be stopped and started anywhere, even after a crash or a usage limit. Each lead also starts its own `caffeinate`. Orlando can pause it by editing the Controls at the top of the roadmap.

## Running on a schedule

The Heartbeat skill adds jobs that run at set times. Each job is one file: when it runs, a free check for whether there is any work, and a checklist for an agent. The agent starts only if the check finds work, and it runs with limits on its tools, its folders, its time, and its cost. One job is a relay watchdog. If a relay that is switched on loses its lead, the watchdog can start a new one.

## Seeing what happened

The Latido app shows what the heartbeat is doing, in a small window.

When Orlando comes back, he has several places to look:

- The roadmap's Log and git hold what each finished batch did.
- `--status` on the relay's start script says where a relay stands.
- `heartbeat log` shows recent job runs and what they cost.
- `heartbeat doctor` names anything that needs him.
- Latido shows the heartbeat at a glance.

## The pieces it joins

- [Relay](https://oneillo.com/ai-setup/skills/relay/)
- [Heartbeat](https://oneillo.com/ai-setup/skills/heartbeat/)
- [Global rules](https://oneillo.com/ai-setup/rules/global/)
- [Latido](https://oneillo.com/ai-setup/apps/#latido)

[Back to AI Setup](https://oneillo.com/ai-setup/)

[Borrowed something? Tell me how it went](https://www.linkedin.com/in/oneillo/)

Text: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Scripts: [MIT](https://opensource.org/license/mit).
