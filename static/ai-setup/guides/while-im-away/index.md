# How work runs while I'm away

- Kind: Claude Code walk-through
- Address: https://oneillo.com/ai-setup/guides/while-im-away/
- Last updated: 2026-11-27

> AI-written and cleaned from one person's working setup. Placeholders in angle brackets are not real values: ask your user for theirs. The newest version is at https://oneillo.com/ai-setup/guides/while-im-away/.

Shows how the keep-awake rule, the Relay skill, the Heartbeat skill, and the Latido app keep work going and report back while Orlando is away.

When Orlando steps away, several pieces of his setup keep work moving. Each has a different job.

## Staying awake

The Global rules say what happens first. When he steps away, works over Remote Control, or a job runs unattended, the session keeps the Mac awake without being asked. It wraps the long job in `caffeinate -ims`, or starts one background `caffeinate` sized to the work. The screen may sleep. A session stops only the `caffeinate` it started.

The same rules say what to do when he is away from a session that isn't on Remote Control: suggest `/remote-control`. While it is on, the session sends a push notification when work is ready for his feedback or blocked on him. Routine progress never gets one.

## Long plans: the Relay skill

The Relay skill runs a plan as a chain of fresh sessions. The plan is a `roadmap.md` cut into batches. One fresh session leads one batch. It briefs up to three builders, merges their work, runs the project's checks, has a fresh reviewer read the result, delivers, writes it up, and starts the next session.

Nothing waits for Orlando. His decisions sit in the roadmap with recommendations, and those are followed unless he has answered. State lives in the roadmap, `context.md`, and git, so a batch interrupted by a power cut or usage limit is picked up from the files. Each lead also keeps the Mac awake with its own `caffeinate`.

## Scheduled jobs: the Heartbeat skill

The Heartbeat skill manages jobs that run on a schedule. Each job is one file that says when it runs, what the agent should do, and which tools and folders it may use. A job can also have a cheap check. The check decides whether there is any work, so an agent session starts only when there is.

One job is a relay watchdog. It restarts a relay that is switched on if its lead has gone away.

## Finding out what happened

The Latido app shows what the heartbeat is doing: its jobs, when they run, how each run ended, and whether anything is wrong. Its page says nothing more than that.

For a relay, the record is the roadmap's Log and git. A relay's status can also be read at any time, and each lead ends with a short message saying what he can now do and what waits for his decision.

## How they fit

The Heartbeat skill starts scheduled work, and Orlando starts a relay. The keep-awake rule keeps the Mac running while that work goes on. Latido, the roadmap's Log, and the push notifications are how he finds out what happened when he is back.

## The pieces it joins

- [Relay](https://oneillo.com/ai-setup/skills/relay/)
- [Heartbeat](https://oneillo.com/ai-setup/skills/heartbeat/)
- [Global rules](https://oneillo.com/ai-setup/rules/global/)
- [Latido](https://oneillo.com/ai-setup/apps/#latido)

[Back to AI Setup](https://oneillo.com/ai-setup/)

[Borrowed something? Tell me how it went](https://www.linkedin.com/in/oneillo/)

Text: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Scripts: [MIT](https://opensource.org/license/mit).
