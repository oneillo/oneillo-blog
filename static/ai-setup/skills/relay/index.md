# Relay

- Kind: Claude Code skill
- Address: https://oneillo.com/ai-setup/skills/relay/
- Last updated: 2026-11-23

> AI-written and cleaned from one person's working setup. Placeholders in angle brackets are not real values: ask your user for theirs. The newest version is at https://oneillo.com/ai-setup/skills/relay/.

Runs a project's roadmap unattended as a relay of fresh Claude Code sessions, each leading one batch with parallel agents and handing off to the next.

- **What it is:** Relay is a Claude Code skill that runs a project's roadmap while its owner is away, with a fresh session leading each batch.
- **How it starts:** You type `/relay`. Claude can also pick it up when it fits.
- **Where the file goes:** `~/.claude/skills/relay/SKILL.md`, its other files beside it
- **Needs:** Rex, uv, jq, git, and macOS
- **To make it yours:** Put your own values where you see `<amount>` and `<default-budget>`.
  Taken out for privacy: 15 dates and incident specifics, 7 amounts, 1 keychain service name, and 1 private project name

**Works with:**
- **Called by:** [Heartbeat](https://oneillo.com/ai-setup/skills/heartbeat/).
- **Also mentions:** [Corillo](https://oneillo.com/ai-setup/apps/#corillo), [Pregón](https://oneillo.com/ai-setup/apps/#pregon), and [Rex](https://oneillo.com/ai-setup/skills/rex/).

## Files on this page

| File | What it is | Size |
| --- | --- | --- |
| `SKILL.md` | The main guide for the relay skill. | 104 lines |
| `builder-brief.md` | The brief every builder agent in a relay batch reads first, covering where to work, how to prove the work, and what to put in the final report. | 41 lines |
| `lead.md` | The instructions a relay lead session follows to lead one batch of a roadmap, from reading the plan to handing off to the next session. | 93 lines |
| `reviewer-brief.md` | The brief for the fresh reviewer who reads a finished relay batch, with the rules it works under, what to look for, and the form of its findings. | 48 lines |
| `roadmap-template.md` | The blank form for a project's `roadmap.md`, with its Controls, decisions table, batches, and Log, which the relay scripts read. | 58 lines |
| `lead-settings.json` | The settings every relay lead session starts with, which turn any permission question into a "no" so a lead never waits on an answer. | 5 lines |
| `close-pane-later.sh` | Closes a Rex pane once the session in it has been idle for a while, so finished lead sessions don't pile up. `start-lead.sh` starts it, and it can also be run by hand. | 89 lines |
| `memory-fuse.sh` | Watches the Mac's biggest processes while a relay lead runs and stops any single process a lead started that grows past a quarter of the Mac's memory. `start-lead.sh` starts it, and it can be run with `say` to see what it would stop. | 220 lines |
| `sdk-run.py` | Runs one Claude Code session through the Agent SDK on an API key, with a hard spending cap, and logs each run's cost. `start-lead.sh` uses it for relays set to `sdk` billing, and it can be run by hand. | 154 lines |
| `start-lead.sh` | Starts a fresh Claude Code session to lead the next batch of a project's roadmap, puts finished lead panes away, and with options says what it would start or where the relay stands. | 619 lines |
| `watch-relay.zsh` | Watches one relay from another session and exits with an "EVENT:" line when a batch finishes, no lead is running, the relay's sessions go quiet for 30 minutes, or a time limit passes. | 46 lines |

## The skill file (`SKILL.md`)

The main guide for the relay skill. It explains what a relay is, when it fits, how to set one up, and how to start, check, pause, stop, and recover it.

````
---
name: relay
description: Run a plan as an unattended relay of fresh Claude Code sessions. Each session leads one batch of the project's roadmap.md with parallel agents, checks it, delivers it, writes it up, then starts the next session in a new Rex pane with Remote Control on. Use this whenever Orlando wants a roadmap or a long list of work run while he is away, asks to set up "the loop", "the relay" or "the cycle" in a project, wants the automated loop that built Corillo used somewhere else, or asks to start, check, pause, stop or restart one, including after a crash, a power cut or a usage limit.
---

# Relay

A relay works through a project's `roadmap.md` without Orlando at the computer. The roadmap is cut into batches. A fresh Claude Code session leads one batch: it briefs up to three builders, brings their work together, runs the project's checks, has a fresh reviewer read the result, delivers it, writes it up, and starts the next session. Then it ends.

Why it is built this way:

- **A fresh session per batch.** A lead that lives through the whole roadmap costs more every time it wakes, and gets worse as its context fills. So the state lives in `roadmap.md`, `context.md` and git, never in a session's memory.
- **Nothing waits for Orlando.** Every decision that is his sits in the roadmap with a recommendation, and a batch goes by the recommendation unless he has answered. The calls a lead makes are written in the Log for him to look at afterwards.
- **It can be stopped and started anywhere.** A batch left "running" by a power cut or a usage limit is picked up from what is in the files.
- **He can watch and steer.** Every lead runs with Remote Control on, named "<Project>: Batch N", and the Controls at the top of the roadmap are his to change at any time. A relay on `sdk` billing runs without Remote Control (see "Who pays").
- **It tidies up after itself.** In Rex a relay keeps to one tab, named after the session leading now ("Heartbeat: Batch 3"). Each new lead opens as a pane beside the last, and a finished lead's pane closes by itself once it has been idle for 15 minutes, the last lead's included. So the tab holds one pane most of the time and goes when the relay ends. What a session did is in the roadmap's Log and in git, not in an open pane.

`<skill>` below is this file's folder.

| File | For |
|---|---|
| `scripts/start-lead.sh` | Starts the next lead session and puts finished ones away, or says where the relay stands |
| `scripts/close-pane-later.sh` | Closes a finished lead's pane once it is idle. `start-lead.sh` starts it. With `--say` it only says what it would do |
| `scripts/memory-fuse.sh` | Stops a single runaway process that a lead or its agents started, before macOS runs out of memory. `start-lead.sh` starts it, and it ends by itself. With `say` it only says what it sees |
| `scripts/watch-relay.zsh` | Watches one relay from another session and exits with an "EVENT:" line when a batch finishes, no lead is running, or the relay goes quiet for 30 minutes. Costs nothing while it waits |
| `tests/memory-fuse.test.zsh` | Proves the fuse on made-up processes in about 30 seconds. Run it after changing the fuse, or after a macOS update |
| `tests/start-lead.test.zsh` | Proves how `start-lead.sh` reads a roadmap, on made-up roadmaps: what it starts, and that text in a Log can't steer it. Starts nothing. Run it after changing that script |
| `lead-settings.json` | Settings every lead starts with: a permission question becomes a "no", so a lead never waits all night on one. Leads only, not Orlando's own sessions |
| `scripts/sdk-run.py` | Runs one session through the Agent SDK on Orlando's API key, with a budget cap. `start-lead.sh` uses it when the Controls say `- Billing: **sdk**`; it works by hand too (`uv run <skill>/scripts/sdk-run.py --help`) |
| `lead.md` | What a lead session does. A lead reads it; you don't need to unless you are changing how leads work |
| `builder-brief.md`, `reviewer-brief.md` | What every builder and reviewer is told |
| `roadmap-template.md` | The form of `roadmap.md` |

## Does a relay fit?

Say so before setting one up if it doesn't. A relay fits work that:

- can be cut into batches of two or three items that don't depend on each other;
- has a check that says "done" without Orlando: tests, a build, a fresh reader holding the result against what was promised;
- is worth the usage. Each batch is a lead, up to three builders and a reviewer.

It doesn't fit work that needs his judgment at each step (writing in his voice, a decision with real stakes), or anything one session can finish. For "check on something every few minutes", `/loop` is the tool.

## Set one up

1. **Agree the plan with Orlando first.** The roadmap is his: what gets built, in what order, and what is left out. If no plan exists, make one with him (one question per message), or from reviews he has asked for. Never queue work he hasn't agreed to.
2. **Write `roadmap.md`** at the project's root from `<skill>/roadmap-template.md`. What makes a roadmap run well unattended:
   - Each item says what the person gets, how big it is, and which files are its own, so two builders never edit the same file.
   - Items in one batch are independent. Work that builds on other work goes in a later batch.
   - Every decision that is Orlando's is in the table with a recommendation. Anything that truly needs him (an account, a purchase, a judgment only he can make) goes under "Later", or in a batch marked `Status: **queued, low**`, which the relay skips.
   - "How this project runs" is filled in from the project itself: find the real command for the checks and run it once, so the first lead doesn't discover that it fails. Write down the delivery step and whether Orlando has agreed it may be done without asking.
   - **Keep the template's form for what the scripts read.** The relay is on only when the `## Controls` section has one Relay line and it is exactly `- Relay: **on**`, with nothing after it (a note goes on a line of its own). A batch's `Status:` is the line right under its `### Batch N` heading, in the `## Batches` section. Anything written another way reads as off or not there, and nothing starts. That is on purpose: a Log entry, a quoted line or a pasted example must never be able to switch a relay on or queue a batch (a security review of the heartbeat found this). The heartbeat's watchdog reads a roadmap the same way.
3. **Make sure the project can carry it.** Code needs its own git repo for builders to work side by side in worktrees (follow the workspace's rules for a repo inside `~/ai`). The project needs a `CLAUDE.md` and a `context.md`: every lead and builder starts from them. The folder must be one Claude Code has been used in before, or the new session stops at a question about trusting it. A new git repo gets that question even inside `~/ai`: trust goes by the repo's root. Who answers it depends on who made the folder:
   - **A folder you made yourself inside `~/ai`, in this same sitting** (your own `git init`, and nothing cloned, downloaded or unpacked in it): answer it yourself. Orlando's rule: "~/ai is my agent workspace. You all have permission to do anything you want in that folder including anything contained within it." Open a plain `claude` there in its own Rex pane without focus (`rex run --json --focus=false --label "<Name>: trust this folder" -c <project folder> -s "$REX_SESSION" -- ~/.local/bin/claude`), read the pane (`rex capture -b <block id>`) and go on only if it shows the question. Send the Down arrow (`rex send -b <block id> $'\e[B'`), read the pane again to see "Yes, I trust this folder" picked, send Return (`$'\r'`), and check that Claude Code recorded it: `jq '.projects["<full path>"].hasTrustDialogAccepted' ~/.claude.json` says `true`. Then end that session (`/exit`, Return) and say in the project's log that you answered it. Never write to `~/.claude.json` yourself.
   - **Any other folder** (a clone, a download, something another program made, anything outside `~/ai`): only Orlando answers, at the Mac's keyboard, not from his phone. Open the pane the same way, name its tab (`rex window rename <window id> "<Name>: trust this folder" -s <session>`), and put on his list: that tab, Down arrow once, Return.
4. **Dry run:** `<skill>/scripts/start-lead.sh <project folder> --dry-run` says which batch would start and under what name.
5. **Tell Orlando what will happen and what it will cost** (how many batches, how many agents in each), then start it on his word.

## Start, check, pause, stop

- **Start, or start again:** `<skill>/scripts/start-lead.sh <project folder>`. It starts the first batch that is running or queued, never a second lead for the same project, and nothing if the Controls say the relay is off. Then read the new pane once with the `rex capture` command it prints, to see that the session began work.
- **Check:** `<skill>/scripts/start-lead.sh <project folder> --status` lists the Controls, each batch's status, whether a lead is running and what its pane reports ("working", or "idle for 2 h 10 min"), and what a new lead would start on. A line it can't read (a batch's Status, Stop after batch, Lead, Billing, and on sdk Lead budget) is named there, and nothing starts until it is fixed. The roadmap's Log has what each finished batch did. To see what a lead is doing right now, read its pane with `rex capture`, or tell Orlando the session's name so he can open it in the Claude app.
- **Pause after a batch:** set "Stop after batch" in the Controls to that batch's number. To stop when the current batch ends, set Relay to **off**. A lead reads the Controls again just before it hands off.
- **Stop now:** tell the live lead to stop cleanly (type into its pane with `rex send`, or Orlando tells it over Remote Control). It commits what is finished, writes where things stand, and ends. Closing its pane instead leaves the batch "running" with its work in the files, which the next start also picks up, but any agents in flight are lost. If the heartbeat's relay watchdog watches the project (its job file lists the folder), a relay that is on is started again within about an hour of its lead going away, so set Relay to **off** first when the stop is meant to last.
- **Add work:** only what Orlando has agreed. His bugs and ideas for an app wait in the `feedback.md` at its root; a new batch starts from the open ones there and marks each one it builds. If a lead is running, tell it and let it write the batches in at its write-up, so two sessions don't edit the roadmap at once. Otherwise add them to `roadmap.md` in the template's form.
- **Telling a live lead something:** read its pane first (`rex capture -b <pane>`), and never type while it shows a permission question. Then `rex send -b <pane> '<one short line>'` and `rex send-key -b <pane> enter`. Say which session is writing and that Orlando did not type it. Anything longer goes in a file the lead can read, and the line points at it: a note of about 2,700 characters typed this way once arrived as its last 30 characters, and a line of 220 arrived whole. Read the pane again afterwards to see that the lead answered what you sent.

A project that already has its own launcher keeps using it until Orlando asks to move it over to this skill. Corillo does (`scripts/next-orchestrator.sh`): its leads follow the project's own prompt under their own name, so this script would not see them and could start a second lead.

Each lead keeps the computer awake with its own `caffeinate -ims` (the display may sleep). Two relays can run at once in different projects, but they share Orlando's usage and the computer, so start a second only when he asks.

One memory fuse watches every relay on the Mac. A single process that a lead or its agents started is stopped once it passes a quarter of the Mac's memory (4 GB of 16). The lead gets one line in its pane, and a note goes in `~/.claude/relay-fuse.log`. It exists because a test once fed a very long answer to a shell line whose memory grows with the square of its input: one process asked for many times the Mac's memory within seconds, and macOS raised its out-of-memory alarm over and over while most memory was free. What the fuse does not limit: many middling processes at once, the processor, and anything no relay lead started (this session, a heartbeat job).

## Who pays

The Controls' Billing line picks one of three modes. Change it only on Orlando's word. Each lead starts the next with `start-lead.sh`, so the line holds until someone changes it. `--status` shows it, and a Billing line it can't read starts nothing.

- **plan** (or no line): his plan, with Remote Control, as everything above describes.
- **sdk**: his API key through the Agent SDK, which his plan's promotional credit covers (Claude Code itself is not covered). The lead runs as `uv run <skill>/scripts/sdk-run.py` in the relay's Rex tab, with the same first message, model, effort, `lead-settings.json` and permission mode auto, and with user, project and local settings loaded. Its cap is the Controls' `- Lead budget: **<amount>**` line (in US dollars, with a default when there is no line); a lead that spends it is stopped, and `sdk-run.py` adds an entry to the Log. Every run's cost goes to `~/.claude/sdk-costs.log`. Claude Code reads the key from the Keychain itself, through an `apiKeyHelper` that `sdk-run.py` passes in the run's settings, so the key is never in the environment of the commands, hooks and MCP servers the lead starts, and auto mode stays on (the `CLAUDE_CODE_SUBPROCESS_ENV_SCRUB` route forces permission mode to default, so don't use it). There is no Remote Control: Orlando reads the lead's pane in Rex, or the Log. Nothing typed into its pane reaches it, so "Telling a live lead something" and the memory fuse's line in its pane don't work: to stop one, set Relay to **off** (it stops at its next hand-off) or close its pane.
- **api**: refused, nothing starts. It ran interactive `claude` with an `apiKeyHelper`, which is billed as Claude Code, not covered by the credit: it once drained his paid balance until every call was refused.

The heartbeat's relay watchdog checks the plan's `/usage` before it starts a lead, so while the plan is at its limit it does not restart an sdk relay either: start that one by hand. It does restart an sdk lead stopped at its budget once no lead is alive, so each restart can spend another Lead budget (it starts a limited number a day).

## After a crash, a power cut or a usage limit

Look first: `--status`, and in a git repo `git log --oneline -5` and `git worktree list`. Then one of:

- **A usage limit, and the lead's pane is still open.** A lead stopped by a limit in the middle of a turn stays alive and idle: `--status` shows it as running, "idle for" a long while, and `start-lead.sh` refuses every new start while it sits there. Remote Control sessions don't resume by themselves after a limit. After the reset, either send it one line to carry on (see "Telling a live lead something"), or close its pane (`rex block close <pane>`) and start again as below.
- **Start again** with `start-lead.sh`, once no lead of this relay is alive. A fresh lead carries on the batch marked "running" from what is in the files.
- **Bring the stopped lead back,** when it stopped a short while ago with builders in the middle of their work. Resuming keeps the lead's context and lets it continue each builder with its own, at the price of reading all of that context once more. Find the session's id (the newest `.jsonl` in the project's folder under `~/.claude/projects/`), then start it the way the script would, so it is named and guarded like any lead:

  ```
  rex run --focus=false --label "<Name>: Batch N" -c <project folder> -- ~/.local/bin/claude --remote-control "<Name>: Batch N" --permission-mode auto --settings <skill>/lead-settings.json --resume <session id> "<one plain line: what happened, what was lost, carry on>"
  ```

  An sdk lead can't be resumed this way: start a fresh one.

  Tell it that everything running at the time is gone (its builders, its `caffeinate`, any checks in flight), and to continue each builder with SendMessage.

Either way, confirm with `rex capture` that the session came up and began work, and that it started its own `caffeinate`. `--status` says whether the memory fuse is watching: a lead brought back by hand has none until you start it, with `( <skill>/scripts/memory-fuse.sh watch </dev/null >/dev/null 2>&1 & )`. A session brought back by hand opens in a tab with no name: name it with `rex window rename <window id> "<Name>: Batch N"` (the id is in `rex window ls`), so Orlando can tell his tabs apart.

## What it needs

Rex (the sessions run in its panes), Remote Control (on the plan), `uv` (on sdk), permission mode "auto" (the lead isn't asked before each step, so the project's `CLAUDE.md` must say what is off limits), `caffeinate`, and `jq` (part of macOS), which names the relay's tab and finds finished panes. It has been proven on one code project, Corillo. On other kinds of work, treat the first batch as a trial and read its Log entry before letting the rest run.
````

## `builder-brief.md`

The brief every builder agent in a relay batch reads first, covering where to work, how to prove the work, and what to put in the final report.

```
# Brief for every builder in a relay batch

You are one of up to three agents working on the same project at the same time. The lead (another Claude session) gave you one item and will bring your work together with the others'. Orlando, the owner, isn't there to answer questions: make sound calls yourself and write down the ones he should see.

## Read first

The project's `CLAUDE.md`, its `context.md` if it has one, "How this project runs" and your batch in `roadmap.md`, and whatever your prompt names. The project's rules win over this brief. Read long reference files by section (find the part you need with grep on the headings), not whole.

## Where you work

- Only in the place your prompt names: your worktree and branch, or your own files. With a worktree, never the main checkout. Never another agent's files, an installed or live copy of the project, or real data.
- Shell state doesn't carry between commands: start each with `cd <your place> &&`, and use absolute paths with the file tools.
- Scratch files go where the project says, or under `~/ai/tmp/<project>/<your name>/`. Not in the project folder.
- Don't edit `roadmap.md` or `context.md`: the lead writes those from your report.
- Never deliver, install, publish, push or merge. The lead does that once the whole batch passes.

## How to work

- **Build what the item says, no more.** No drive-by rewrites, renames or tidying. If you find a real problem on the way, fix it only if it is small and in your files. Otherwise report it.
- **Prove it.** For code: write the test that shows the thing missing or wrong, see it fail, build, see it pass. Orlando doesn't read code, so tests are his review. For anything else: check the result against what the item promised and say how you checked (the source for each claim, the numbers worked again, the file opened and read).
- **Decisions.** The roadmap's table decides, and a decision without an answer goes by its recommendation. For a small call nobody covered, choose what Orlando would most plausibly want, keep it small, and list it under "Calls for Orlando". For a big one, don't build it: report it.
- **Other agents are working on this computer right now.** While iterating, run only the checks you need. Before you finish, run the project's full checks once. A check that measures time and fails under load: run it alone twice before deciding, and say so in your report rather than changing it. Never loosen or delete a check to get a pass. If a check is wrong, say why.
- **Never find a limit by hitting it.** A command whose memory grows with its input can ask for hundreds of GB within seconds. macOS then raises its out-of-memory alarm for the whole Mac and stops other programs, while most memory is still free. So when your item is about very large input, hostile input or load: measure at three or four small sizes (`/usr/bin/time -l <command>` prints the peak memory), work out the rest, and prove the limit with a test at a small size. Small means a peak under 200 MB. One size per command, smallest first, never a loop over sizes, and stop the series at the first size that passes 500 MB or is stopped. A command that ends with exit code 137 or "Killed: 9" was stopped for memory, by macOS or by the relay's memory fuse (it stops any one process that passes a quarter of the Mac's memory). Never run it again as it was: make it smaller, and say in your report what happened.
- **Never use `git stash`.** It is shared by every worktree of a repo, so one agent's stash can land in another's work. To set work aside, make a temporary commit on your branch.
- **Clean up after yourself.** Whatever you made that isn't the deliverable (temporary folders, test data, builds, downloads, programs you started) is gone before you finish. Leftovers that grow with use are a leak.
- **Never take the computer's focus.** Don't bring any app to the front.
- **Never put Orlando's name, email or file paths in a request to an outside service.**
- **Words people read** are plain and short, with no em dashes.
- **In git, commit as you go** on your branch, with short subject lines that say what the person now gets. Don't merge, rebase or touch the main branch.

## Your report (your final message)

Under 400 words:

1. **Built:** each item, one line on what the person now gets, and the check that proves it.
2. **Not done:** what, and why.
3. **Calls for Orlando:** each call you made: what you chose, why, and what the other option was.
4. **Checks:** the result of your last full run, and anything that failed under load but passed alone.
5. **Left over:** anything the lead should pick up.

Say plainly what is verified and what isn't. If the full checks don't pass, say so and name what fails. Don't present unfinished work as done.
```

## `lead.md`

The instructions a relay lead session follows to lead one batch of a roadmap, from reading the plan to handing off to the next session.

```
# Leading one batch of a relay

You are the lead for exactly one batch of this project's `roadmap.md`. A script started you in a fresh session. Orlando isn't at the computer and may look in over Remote Control, so keep your messages short and lead with anything he needs to know.

Each batch gets a fresh lead so that no session's context grows through the whole roadmap. That only works if everything the next lead needs is in the project's files and in git, not in your head. Write things down as you go.

The project's own rules win over this file: its `CLAUDE.md`, and the "How this project runs" section of `roadmap.md`.

`<skill>` below means the folder this file is in.

## Before anything else

1. **Keep the computer awake.** Start `caffeinate -ims -t <seconds>` in the background, sized to the batch (6 hours if you can't tell), and note its process id. Start your own even if another is running: another session stops its own before your batch ends. The display may sleep: Remote Control doesn't need it (Orlando's call).
2. **Read** the project's `CLAUDE.md`, its `context.md` if it has one, and `roadmap.md`: the Controls, "How this project runs", the decisions, your batch, and the Log's last entry. Don't read long reference files whole (a spec, research notes): the builders read the parts they need.
3. **Find your batch:** the first one whose status is "running" or "queued". One marked "queued, low" is skipped.
4. **Look at where things stand.** In a git repo: `git status`, `git log --oneline -15`, `git worktree list`, `git branch`, and `git status --short` in each worktree. Anywhere: which of the batch's files already exist, and free disk space (`df -h`).
   - If the batch is "queued", mark it `Status: **running** (<today's date>, session "<your session's name>")` and commit that. The Status stays where it is, on the line right under the batch's heading: the scripts read it nowhere else.
   - If it is "running", an earlier session was stopped part way (a power cut, a usage limit). First count the pick-ups: if this pick-up finds nothing new since the one before (no commit, no changed file, no Log line), write `(pick-up 2)` in the parentheses after `**running**`, never inside the bold, and add one at each such pick-up in a row; a pick-up that finds progress clears the count. At the third in a row, set Relay to **off**, say why in the Log, commit, and stop cleanly as "Keep the cost down" says. Otherwise carry on from what is there: what is merged, the worktrees and their branches, uncommitted work, the Log. Don't start the batch again. That session's agents ended with it, so for each unfinished item brief a fresh builder to finish from what is there: tell it what is committed, what isn't, and that any check that was running must be run again.

## Do the batch

1. **Builders.** One agent per numbered item, at most three at once, with the model and effort the Controls give (Agent tool). An item that is a few minutes' work you may do yourself. Each builder's prompt:
   - read `<skill>/builder-brief.md` first, then the project's rules;
   - its item, quoted from `roadmap.md`, and the notes it points to;
   - where it works, the files that are its own, and the files the other builders are in;
   - the decisions that apply (a decision Orlando hasn't answered goes by its recommendation);
   - what the other builders' work will show where the two meet. Message a builder that is still running when another's work lands and changes what it will see.

   Where they work depends on the project:
   - **Code in its own git repo:** each builder in its own worktree under `.claude/worktrees/<name>`, on its own branch (`relay/b<batch>-<short-name>`). Link in large untracked folders (such as `node_modules`) rather than installing them again.
   - **Anything else** (documents, research, a folder inside a larger repo): no worktrees. Each builder writes only its own files.

   Builders never deliver, never merge, and never edit `roadmap.md` or `context.md`. You do that from their reports.
2. **Bring it together.** With worktrees: merge each branch with `git merge --no-ff` when its builder reports its checks green. Settle small conflicts yourself (both sides added something: keep both) and send large ones back to the builder. Build straight after each merge: two branches can merge without conflict and still not work together. Then run the project's full checks once, on the whole. Failures that no builder had alone are expected: find the cause, never loosen a check.
3. **Review.** One fresh reviewer, read-only, on everything the batch changed, briefed with `<skill>/reviewer-brief.md` plus what to review (the commit range, or the files) and where to write its findings: a scratch file outside the project, unless the project names a place. What matters from it goes in the Log, so the file needn't be kept. Fresh eyes find real problems in work that passes its checks, so don't skip this for a batch that built something. Give what is serious to one fix agent, then run the checks again. List the rest in the Log. If the reviewer couldn't write its findings file, its last message carries them.
4. **Deliver.** When the checks pass on the final state, do the delivery step that "How this project runs" describes, if it says that step may be done without asking. If it doesn't say, don't deliver: write in the Log that the batch is ready and what delivering it would take.
5. **Write it down.**
   - In `roadmap.md`: the batch's `Status: **done** (<date>; <the result in a few words>)`, and an entry in the Log: what was built, what was left and why, the calls you made for Orlando (what you chose, why, and the other option), what was delivered, and what he should try by hand.
   - `context.md`, if the project has one, brought up to date: the state, decisions, where things are. Keep it short.
   - Work you think should come next goes in the Log as a suggestion. Only Orlando adds batches to the queue. If he has told you to queue something, write it as a batch in the form the others have.
   - Commit, with only this project's files. Never `git add -A` in a repo that holds other work.
6. **Clean up.** Remove the worktrees and merged branches. Leave no temporary folders, test data or builds, and no programs running: look for what your agents started. Check free disk space again.

## Decide without him

Orlando isn't there, so nothing waits for him. A decision in the roadmap's table with no answer goes by its recommendation. For a call nobody covered, choose what he would most plausibly want, keep it small and easy to undo, and put it in the Log. A call that is big or can't be undone isn't yours: leave that item, say why in the Log, and go on with the rest.

## Keep the cost down

It is Orlando's usage. Use no more agents than the batch needs, no second full run of the checks where one will do, and short messages.

**Spend usage well** (Orlando):

- A test run that passes should cost a few lines: the runner's quiet reporter (for vitest, `--reporter=dot`), or the full output sent to a file in the temp folder with only its last 40 lines printed. Read the file only when a run fails, and remove it afterwards.
- The agent that needs a full suite runs it and reports the counts and the names of what failed. A lead never pastes a suite's output into its own conversation.
- Read the part of a file the work needs, not the whole of a long one twice.
- Unless a roadmap's Agents line says otherwise: builders and fix agents on Opus 5.5 at effort high, a batch's one reviewer at xhigh.

**On the API key (sdk).** When the Controls say `- Billing: **sdk**`, you run through the Agent SDK (`<skill>/scripts/sdk-run.py`) on Orlando's API key, not his plan: his plan's promotional credit covers it, and every token is money, so the rules above count double. Your agents run in your process and bill the key too, with nothing for you to set. You have a hard cap, the Controls' `- Lead budget` (in US dollars, with a default when there is no such line): when it is spent you are stopped mid-turn with no chance to write anything, and `sdk-run.py` adds a short entry to the Log saying so. So commit finished work as you go, and keep the Log's account of where things stand current at each step, not only at the end. You have no Remote Control (it needs the plan), and nothing typed into your pane reaches you: Orlando reads your pane in Rex and the Log. Your cost is written to `~/.claude/sdk-costs.log` when you end; in your Log entry say that's where it is. Never read, print or pass the key, and never run the Keychain command yourself: Claude Code reads it through the `apiKeyHelper` that `sdk-run.py` sets, and the commands you run don't see it. The next lead keeps the Billing and Lead budget lines as they are: change them only on Orlando's word.

**When an action is denied.** You run with permission questions turned into a "no" (the skill's `lead-settings.json`), so nothing waits for Orlando. When an action the batch needs is denied, don't look for a way round it: finish what can be finished, say in the Log what was denied and why it was needed, and stop cleanly if the batch can't be done without it.

If you are about to run out of usage, or the batch is going badly: stop cleanly. Commit what is finished and passing, write in the Log exactly where things stand, leave the status "running", stop your `caffeinate`, and run `<skill>/scripts/start-lead.sh <project folder> --close-me`. That starts nothing while your batch is marked running. It puts your pane away once you have been idle for 15 minutes, so no stopped session is left open in Rex. Then write your last message and end.

The next start carries on from there, and it may come without anyone asking: if the heartbeat's watchdog watches this relay, it starts a fresh lead once no lead is alive and the usage allows. So if a fresh lead would only meet the same trouble, also set Relay to **off** in the Controls and say why in the Log.

## Keep the computer usable

It is Orlando's own Mac. His apps and other relays share it. A process that grows without bound can raise macOS's out-of-memory alarm for the whole Mac within seconds, while most memory is still free: a test of a very long answer once did that nine times.

- **The memory fuse** (`<skill>/scripts/memory-fuse.sh`) started with you. It stops any single process that you or your agents started once it passes a quarter of the Mac's memory, and types one line into your pane when it does. When that line comes: tell the agent whose command it was, see that the command isn't run again as it was, and say in the Log what was stopped and why it grew. `<skill>/scripts/memory-fuse.sh say` shows what it sees.
- **Exit code 137, or "Killed: 9",** means a command was stopped for memory, by the fuse or by macOS. The same goes: never again as it was.
- **Limits are tested small.** An agent whose item is about very large input, hostile input or load must not find a limit by hitting it. The builder brief says how; point to that rule in its prompt.

## Never

- Take the computer's focus: no app brought to the front, panes opened with `--focus=false`.
- Put Orlando's name, email or file paths in a request to an outside service.
- Push, rewrite git history, or delete what this relay didn't create.
- Stop another session's `caffeinate` or programs: only your own, by process id.

## Hand off

Every lead ends the same way, whether or not another batch follows, so no finished session is left open in Rex for Orlando to close or to tell apart.

1. Read the Controls again: Orlando may have changed them while you worked. If the relay stops here (Relay is off, your batch is the one to stop after, or no other batch is queued), write in the Log why, and commit.
2. Stop your `caffeinate` (the next lead starts its own).
3. Run `<skill>/scripts/start-lead.sh <project folder>` once, in every case. It reads the Controls itself:
   - If another batch is to run, it starts the next lead in a pane beside yours, in this relay's Rex tab, says that the session came up, and prints the command that reads its pane. Read that pane once to see that it has begun work.
   - If not, it starts nothing and says why.

   Either way it puts your session away: your pane closes by itself once you have been idle for 15 minutes.
4. Finish with a short message for Orlando: what he can do now that he couldn't before, what he should try by hand, and what waits for his decision. Then end your turn and do nothing more.
```

## `reviewer-brief.md`

The brief for the fresh reviewer who reads a finished relay batch, with the rules it works under, what to look for, and the form of its findings.

````
# Brief for the reviewer of a relay batch

A few agents have just built one batch of work, each on its own item, and the lead has brought their work together. Every item came with its own checks and they pass. But each builder saw only its own part, the parts had never been together until now, and nobody has read the result with fresh eyes. Work that passes its checks still hides real problems. Finding them before the batch is delivered is your job.

Your prompt says what to review (a commit range, or a list of files) and where to write your findings. Read the project's `CLAUDE.md` and `context.md` first, then the batch's entry in `roadmap.md` with the notes it points to: they say what the person was promised. The project's rules win over this brief.

Judge the batch twice: is the work right, and does the person get what the item promised?

## Rules

- **Read-only.** Don't edit, create or delete anything in the project, and don't check out other commits there. Use `git show` and `git diff` to see other versions. The one file you write is your findings.
- **Don't run the project's build, checks or app:** they belong to the lead right now. To test a suspicion you may run a small scratch script outside the project, and remove it when you finish. Keep it small in every way: never feed a program a huge input to see what happens.
- Never put Orlando's name, email or file paths in a request to an outside service.

## How to review

Read what changed in full, with what is around it, not only the changed lines: most mistakes in a change are in what it didn't look at. Then look for, in this order:

1. **Mistakes the batch introduced:** wrong results, lost or damaged content, an edge the work forgot (empty, very long, unusual characters, two things at once), a failure that now says nothing.
2. **Parts that don't fit together.** Different agents' work, joined without having been designed together. Read it as one piece and say where the parts disagree, repeat each other or leave a gap.
3. **Leaks:** anything added that holds memory, files, disk space or a running program and never lets go, or grows without bound.
4. **Slowness:** anything added that runs often and grows with the size of the data.
5. **Safety,** where the work touches it: a way round a check, or something a hostile file or input could now do.
6. **Words people read** that this batch added: unclear, technical, inconsistent with the rest, or untrue.
7. **Checks that prove nothing:** a new test that would pass with the work removed, an old check loosened or skipped, a claim with no source.
8. **The record:** something written in the spec, the docs or the roadmap that the work doesn't do.

For work that isn't code (a document, research, an analysis), the same order holds: what is wrong, what doesn't fit together, what is claimed without support, and what was promised and isn't there.

Don't report style, naming, or what you would have done differently.

## Output

Write your findings to the file your prompt names, most serious first:

```
### <ID>: <short title>
- Severity: P0 (loss of data, safety) | P1 (a problem people will hit) | P2 (an edge case, a leak, slowness, wrong words) | P3 (minor)
- Where: path:line (every place involved)
- What happens: the concrete failure, as steps or inputs and the wrong result
- Confidence: reproduced | read (certain from reading) | suspected (say what would confirm it)
- Fix: the direction you would take
- Check: what would catch it
```

Start the file with one sentence: would you let Orlando use this as it is, and if not, what must be fixed first. End it with "Looked at and found sound": one line for each part you read closely and found right.

Be honest about confidence, and don't pad: if the batch is sound, a short file is the right answer. Your final message to the lead, under 200 words: the count by severity, the most important findings as one line each with their ids, and anything the lead must know. If you couldn't write the file, put the findings in that message instead.
````

## `roadmap-template.md`

The blank form for a project's `roadmap.md`, with its Controls, decisions table, batches, and Log, which the relay scripts read.

```
# <Project> roadmap

<Two or three sentences: where this plan came from, and the steer from Orlando it follows.>

This file is also the queue. A fresh Claude session leads one batch, checks it, delivers it, marks it done here, then starts the next session (the `relay` skill).

## Controls (Orlando edits these, or tells the live session)

- Name: **<Project>** (sessions are named "<Project>: Batch 1", and so on)
- Relay: **on**
- Stop after batch: **none** (write a batch number in the bold to pause after it, such as **2**; any other form starts nothing)
- Agents: <model> at <effort>, at most three builders and one reviewer per batch
- Lead: **opus high** (the lead's model and effort, so its cost doesn't move with the level saved elsewhere; **default** uses the saved ones)
- Billing: **plan** (who pays for the leads: **plan**, with Remote Control, or **sdk**, Orlando's API key through the Agent SDK, without it)
- Lead budget: **<amount>** (US dollars, the most one lead may spend on **sdk**; the plan ignores it)

To stop the relay after the current batch, change **on** to **off** in the Relay line. Keep that line to those words alone: with anything else on it, nothing starts.

## How this project runs

- **Checks:** <what must pass before a batch counts as done: a command such as `npm test`, or what a fresh reader must confirm>
- **Deliver:** <what happens when a batch passes, and whether it may be done without asking. "Nothing" if there is no such step.>
- **Where builders work:** <worktrees of this repo, or each in its own files; the scratch folder; anything to link in rather than install again>
- **Never:** <what no session may do here>

## Decisions that are yours

Each has a recommendation. A batch that depends on one goes ahead with the recommendation unless you change the answer here.

| # | Decision | Recommendation | Your answer |
|---|---|---|---|
| D1 | **<The decision, in a few words.>** <What it changes for you.> | <Yes, No, or the option> | (recommended) |

## Batches

Sizes: S is one agent's short piece of work, M two or three times that, L a batch of its own.

### Batch 1: <what you get from it>
Status: **queued**

1. **<Item>** (needs D1). <What you see today, and what you will see.> Size S. Owner of: <the files or folders that are this item's own>.
2. **<Item>.** <...> Size M. Owner of: <...>.

Check before delivering: <anything beyond the usual checks, or leave this line out>

### Batch 2: <what you get from it>
Status: **queued**

1. **<Item>.** <...>

### Later
Status: **not queued**

- <Work that is wanted but not yet agreed, or that needs Orlando: an account, a purchase, his judgment.>

## Log

(Each session adds an entry when its batch is done: what was built, what was left and why, the calls made for Orlando, what was delivered, and what he should try by hand.)
```

## Scripts

These scripts are one person's working code, shared from Orlando's setup with private details replaced by placeholders. They are not a supported tool: read each one before you run it, and expect to change it to fit your own setup.

### `lead-settings.json`

The settings every relay lead session starts with, which turn any permission question into a "no" so a lead never waits on an answer.

```
{
  "permissions": {
    "denyInsteadOfPrompting": true
  }
}
```

### `close-pane-later.sh`

Closes a Rex pane once the session in it has been idle for a while, so finished lead sessions don't pile up. `start-lead.sh` starts it, and it can also be run by hand.

**Needs to run:** Rex, jq, and macOS.

```
#!/bin/zsh -f
# Closes a Rex pane once the session in it has been idle for a while. This is
# how a finished lead session is put away (start-lead.sh starts it), so old
# sessions don't pile up for Orlando to close by hand.
#
#   close-pane-later.sh <block id> [idle seconds, default 900]
#   close-pane-later.sh <block id> [idle seconds] --say   say what it would do now, and do nothing
#
# It runs on its own, in no pane. A timer in a tab of its own was one more tab
# to wonder about, and closing that tab stopped the clean-up.
#
# "Idle" is read from what Claude Code reports to Rex about the pane. A pane
# is never closed while its session is working, and the wait is counted from
# the end of its last turn, so a finished lead that Orlando asks something is
# not cut off while it answers. A pane that reports nothing is closed once the
# wait has passed. It gives up after six hours, and ends at once if the pane
# has gone.
zmodload zsh/datetime
trap '' HUP

say=0
args=()
for a in "$@"; do
  if [[ "$a" == --say ]]; then say=1; else args+=("$a"); fi
done
block="${args[1]}"
wait="${args[2]:-900}"
[[ -n "$block" ]] || { print -u2 "Which pane? Give its block id."; exit 1 }
[[ "$wait" == <-> ]] || { print -u2 "The wait is a number of seconds."; exit 1 }

rex_bin=$(command -v rex) || rex_bin="$HOME/Library/Application Support/com.superlogical.rex/RexServer/Versions/Current/Rex Server.app/Contents/MacOS/rex"
jq_bin=$(command -v jq) || jq_bin=""

started=$EPOCHSECONDS
give_up=$(( started + 21600 ))
misses=0

while (( EPOCHSECONDS < give_up )); do
  # What the pane reports. No answer means the pane has gone or Rex isn't
  # running: look twice more, then there is nothing left to close.
  if ! st=$("$rex_bin" block call -b "$block" program_status --autostart=false 2>/dev/null); then
    (( misses += 1 ))
    if (( say || misses >= 3 )); then
      (( say )) && print "The pane has gone, or Rex isn't answering. Nothing to do."
      exit 0
    fi
    sleep 20
    continue
  fi
  misses=0

  count=0 working=0 last=""
  if [[ -n "$jq_bin" ]]; then
    print -r -- "$st" | "$jq_bin" -r '[.records[]?] as $r | "\($r | length) \([$r[] | select(.state == "working")] | length) \([$r[] | .updated_at // empty] | max // "")"' 2>/dev/null | read -r count working last
  fi

  now=$EPOCHSECONDS
  if [[ "$count" != <-> ]] || (( count == 0 )); then
    left=$(( started + wait - now ))
    why="the pane reports no status, so this is a plain timer"
  elif (( working > 0 )); then
    left=$wait
    why="its session is working"
  else
    # The end of its last turn, in this Mac's own time: "2026-11-12T06:47:19...".
    idle=-1
    if [[ -n "$last" ]] && strftime -r -s at '%Y-%m-%dT%H:%M:%S' "${last[1,19]}" 2>/dev/null; then
      idle=$(( now - at ))
    fi
    if (( idle < 0 )); then
      left=$(( started + wait - now ))
      why="its last turn's time can't be read, so this is a plain timer"
    else
      left=$(( wait - idle ))
      why="idle for $idle seconds"
    fi
  fi

  if (( left <= 0 )); then
    if (( say )); then print "Would close it now ($why)."; exit 0; fi
    "$rex_bin" block close "$block" --autostart=false >/dev/null 2>&1
    exit 0
  fi
  if (( say )); then print "Would wait $left more seconds ($why)."; exit 0; fi
  (( left > wait )) && left=$wait
  (( left < 5 )) && left=5
  sleep $left
done
exit 0
```

### `memory-fuse.sh`

Watches the Mac's biggest processes while a relay lead runs and stops any single process a lead started that grows past a quarter of the Mac's memory. `start-lead.sh` starts it, and it can be run with `say` to see what it would stop.

**Needs to run:** Rex, jq, and macOS.

```
#!/bin/zsh -f
# The relay's memory fuse.
#
# While a relay lead is running, it reads the biggest processes on this Mac
# every two seconds. A single process that a relay lead started and that has
# grown past the limit (a quarter of the Mac's memory) is stopped at once,
# with everything it started. The lead is told in one line in its pane, and a
# note goes in ~/.claude/relay-fuse.log.
#
# Why it exists: a test fed a very long answer to a
# shell line whose memory grows with the square of its input. One zsh asked
# for over 200 GB within seconds. Nearly all of it was empty pages, so most of
# the Mac's memory stayed free, but macOS counted them, declared itself out of
# memory, stopped a wave of its own services and showed Orlando its alarm,
# nine times in a quarter of an hour. `ulimit` cannot cap memory on macOS
# (setrlimit fails), so something has to watch.
#
#   memory-fuse.sh watch   watch until no relay lead has run for about ten
#                          minutes. start-lead.sh starts it with every lead,
#                          and a second one ends at once.
#   memory-fuse.sh say     what it sees right now and what it would stop.
#                          It stops nothing.
#
# It only ever stops a process that a relay lead started. Never the lead
# itself, and never anything else on the Mac.
#
# For its tests: "watch --trial" runs beside the real fuse without counting as
# one, and RELAY_FUSE_MB (the limit, in MB), RELAY_FUSE_PATTERN (which sessions
# count as leads), RELAY_FUSE_LOG, RELAY_FUSE_REX, RELAY_FUSE_ROUND (readings
# in a round) and RELAY_FUSE_EVERY (seconds between readings) change what it
# does. A trial always sets its own pattern, so it can never touch a real
# lead's work.
zmodload zsh/datetime
trap '' HUP

self="${0:A}"
mode="${1:-}"
pattern="${RELAY_FUSE_PATTERN:-^([^ ]*claude --(remote-control|name) |[^ ]*uv run [^ ]*sdk-run\.py .*--label ).*: Batch [0-9]+}"
log="${RELAY_FUSE_LOG:-$HOME/.claude/relay-fuse.log}"
limit="${RELAY_FUSE_MB:-$(( $(/usr/sbin/sysctl -n hw.memsize) / 1048576 / 4 ))}"
round="${RELAY_FUSE_ROUND:-150}"
every="${RELAY_FUSE_EVERY:-2}"
if [[ "$limit" != <-> || "$round" != <-> || "$every" != <-> ]]; then
  print -u2 "The limit, the round and the wait are whole numbers."
  exit 2
fi
rex_bin="${RELAY_FUSE_REX:-$(command -v rex)}"
[[ -n "$rex_bin" ]] || rex_bin="$HOME/Library/Application Support/com.superlogical.rex/RexServer/Versions/Current/Rex Server.app/Contents/MacOS/rex"
jq_bin=$(command -v jq) || jq_bin=""

# gb <MB>: the same amount in GB, with one decimal.
gb() { printf '%.1f' $(( $1 / 1024.0 )) }

# note <words>: one dated line in the log, which keeps its last 200 lines.
note() {
  print -r -- "$(strftime '%Y-%m-%d %H:%M:%S' $EPOCHSECONDS) $1" >> "$log"
  if (( $(/usr/bin/wc -l < "$log") > 200 )); then
    /usr/bin/tail -n 200 "$log" > "$log.new" && /bin/mv "$log.new" "$log"
  fi
}

# lead_of <process id>: the relay lead that process runs under. Sets REPLY.
lead_of() {
  REPLY=""
  local q="$1" n=0
  while (( n < 64 )); do
    q=$(/bin/ps -o ppid= -p "$q" 2>/dev/null) || return 1
    q="${q// /}"
    [[ "$q" == <-> ]] || return 1
    (( q > 1 )) || return 1
    if [[ "$(/bin/ps -o args= -p "$q" 2>/dev/null)" =~ $pattern ]]; then
      REPLY="$q"
      return 0
    fi
    (( n += 1 ))
  done
  return 1
}

# name_of <a lead's process id>: its session's name, like "Tasks: Batch 5".
name_of() {
  /bin/ps -o args= -p "$1" 2>/dev/null | /usr/bin/sed -n -E 's/.*--(remote-control|name|label) (.*: Batch [0-9]+).*/\2/p'
}

# family <process id>: everything that process started, then the process.
family() {
  local p
  for p in $(/usr/bin/pgrep -P "$1"); do family "$p"; done
  print -r -- "$1"
}

# pane_of <process id>: the Rex pane that process runs in. Sets REPLY.
pane_of() {
  REPLY=""
  [[ -x "$rex_bin" && -n "$jq_bin" ]] || return 1
  local s b p
  for s in $("$rex_bin" ls --json --autostart=false 2>/dev/null | "$jq_bin" -r '.sessions[]?.session_id' 2>/dev/null); do
    for b in $("$rex_bin" block ls -s "$s" --json --autostart=false 2>/dev/null | "$jq_bin" -r '.blocks[]?.block_id' 2>/dev/null); do
      p=$("$rex_bin" block call -b "$b" process --autostart=false 2>/dev/null | "$jq_bin" -r '.child.pid // empty' 2>/dev/null) || p=""
      if [[ "$p" == "$1" ]]; then REPLY="$b"; return 0; fi
    done
  done
  return 1
}

# tell_lead <a lead's process id> <one line>: types the line into its pane.
tell_lead() {
  pane_of "$1" || return 1
  "$rex_bin" send -b "$REPLY" "$2" >/dev/null 2>&1 || return 1
  "$rex_bin" send-key -b "$REPLY" enter >/dev/null 2>&1
}

# check <process id> <its memory in MB>: stops it if it is over the limit and
# a relay lead started it. Ends with 3 when the process is not this fuse's to
# touch: no relay lead started it, or it is a lead itself.
check() {
  local pid="$1" mb="$2" args lead lead_name short victims line
  [[ "$pid" == <-> && "$mb" == <-> ]] || return 2
  (( mb > limit )) || return 0
  args=$(/bin/ps -o args= -p "$pid" 2>/dev/null) || return 0
  [[ -n "$args" ]] || return 0
  if [[ "$args" =~ $pattern ]]; then
    if [[ "$mode" == say ]]; then
      print "A lead itself is over the limit, and is left alone: process $pid at $(gb $mb) GB."
    else
      note "left alone: a lead itself, process $pid ($(name_of "$pid")), is at $(gb $mb) GB"
    fi
    return 3
  fi
  lead_of "$pid" || return 3
  lead="$REPLY"
  lead_name=$(name_of "$lead")
  short="${${args%% *}:t}"
  victims=($(family "$pid"))
  if [[ "$mode" == say ]]; then
    print "Would stop: process $pid ($short) at $(gb $mb) GB, and $(( ${#victims} - 1 )) it started, under ${lead_name:-lead $lead}."
    return 0
  fi
  kill -KILL "${victims[@]}" 2>/dev/null
  note "stopped process $pid ($short) at $(gb $mb) GB, limit $(gb $limit) GB, under ${lead_name:-lead $lead}, with $(( ${#victims} - 1 )) it had started: ${${args[1,160]}//$'\n'/ }"
  line="Relay fuse: stopped process $pid ($short) at $(gb $mb) GB. One process may use $(gb $limit) GB at most. Don't let it be run again as it was: tell the agent that started it, and put it in the Log."
  tell_lead "$lead" "$line" || note "could not type that into the lead's pane (process $lead)"
  return 0
}

# What is over the limit in what top prints: "<process id> <MB>" a line.
over_limit='
  function mb(s,    u, n) {
    sub(/[+-]$/, "", s)
    u = substr(s, length(s)); n = substr(s, 1, length(s) - 1) + 0
    if (u == "T") return n * 1048576
    if (u == "G") return n * 1024
    if (u == "M") return n
    return 0
  }'

say() {
  local leads p name pid mem any=0
  print "One process under a relay lead may use $(gb $limit) GB at most."
  # -a: macOS's pgrep leaves out its own ancestors otherwise, and this may be
  # run by something a lead started.
  leads=($(/usr/bin/pgrep -a -f "$pattern"))
  if (( ${#leads} )); then
    for p in $leads; do
      name=$(name_of "$p")
      print "Relay lead running: ${name:-process $p} (process $p)."
    done
  else
    print "No relay lead is running."
  fi
  if p=$(/usr/bin/pgrep -f 'memory-fuse\.sh watch$'); then
    print "The fuse is watching (process ${p//$'\n'/, })."
  else
    print "The fuse is not watching. It starts with the next lead."
  fi
  /usr/bin/top -l 1 -o mem -n 25 -stats pid,mem,command | /usr/bin/awk -v limit="$limit" "$over_limit"'
    $1 ~ /^[0-9]+$/ && NF >= 3 { m = int(mb($2)); if (m > limit) print $1, m }' | while read -r pid mem; do
    check "$pid" "$mem" && any=1
  done
  (( any )) || print "Nothing a relay lead started is over the limit right now."
  return 0
}

watch() {
  local other quiet=0 began
  # One fuse for the whole Mac. A trial's fuse neither counts nor checks.
  if [[ "${1:-}" != --trial ]]; then
    sleep $(( ($$ % 10) / 10.0 ))
    for other in $(/usr/bin/pgrep -f 'memory-fuse\.sh watch$'); do
      [[ "$other" == "$$" ]] || exit 0
    done
  fi
  note "watching: one process under a relay lead may use $(gb $limit) GB at most"
  while true; do
    # -a: a lead that started this fuse (by starting the next lead) still counts.
    if [[ -n "$(/usr/bin/pgrep -a -f "$pattern")" ]]; then quiet=0; else (( quiet += 1 )); fi
    if (( quiet >= 3 )); then
      note "ended: no relay lead is running"
      exit 0
    fi
    began=$EPOCHSECONDS
    # A fresh top each round, which ends by itself after its readings. It hands
    # over every reading as it is taken, and each process over the limit is
    # looked at once a round.
    /usr/bin/top -l "$round" -s "$every" -o mem -n 25 -stats pid,mem,command 2>/dev/null | /usr/bin/awk -v limit="$limit" -v self="$self" "$over_limit"'
      $1 ~ /^[0-9]+$/ && NF >= 3 {
        m = int(mb($2))
        if (m > limit && !($1 in seen)) { seen[$1] = 1; system("\"" self "\" check " $1 " " m) }
      }'
    # A round that ended early means top could not run: don't spin.
    (( EPOCHSECONDS - began < round * every / 2 )) && sleep 5
  done
}

case "$mode" in
  watch) watch "${2:-}" ;;
  say)   say ;;
  check) check "${2:-}" "${3:-}" ;;
  *)     print -u2 "Usage: memory-fuse.sh watch | say"; exit 2 ;;
esac
```

### `sdk-run.py`

Runs one Claude Code session through the Agent SDK on an API key, with a hard spending cap, and logs each run's cost. `start-lead.sh` uses it for relays set to `sdk` billing, and it can be run by hand.

**Needs to run:** uv, macOS, and the Keychain (through the `security` command).

```
# /// script
# requires-python = ">=3.12"
# dependencies = ["claude-agent-sdk"]
# ///
"""Run one Claude Code session through the Agent SDK, billed to the API key.

Orlando's plan's promotional API credit covers the Agent SDK but not Claude Code
itself (tested: a run like this one came out of the promo credit,
while interactive claude with an apiKeyHelper was billed as Claude Code; an
SDK run with the same apiKeyHelper came out of the promo credit too).

Usage:
  uv run sdk-run.py --cwd <folder> --budget <usd> [--model M] [--effort E]
      [--label TEXT] [--settings FILE] [--read-dir DIR ...] [--roadmap FILE]
      (--prompt TEXT | --prompt-file PATH)

- Claude Code reads the key from the login Keychain itself (an apiKeyHelper
  setting), so it is never in the environment of the commands, hooks and MCP
  servers the session starts, and auto mode stays on (tested; the
  CLAUDE_CODE_SUBPROCESS_ENV_SCRUB route switches auto mode off). It is never
  printed or written.
- Permission mode is "auto". User, project and local settings load, so deny
  rules and hooks hold; --settings adds one more file (the relay's
  lead-settings.json, which turns a permission question into a "no").
  --read-dir lets the session read one more folder without asking (a Read
  allow rule, nothing more): a relay lead reads its skill's lead.md and
  briefs. Nobody can answer a permission question in an SDK run, so without
  it reading outside --cwd is refused.
- Model and effort: the saved ones unless given.
- --budget is a hard cap: the run stops when it is spent.
- Progress prints to the terminal. One line per run goes to the cost log,
  ~/.claude/sdk-costs.log (SDK_COSTS_LOG overrides it).
- With --roadmap, a run stopped by its budget adds an entry to that
  roadmap's Log (the relay's leads).
"""
import argparse, asyncio, datetime, json, os, subprocess, sys
from claude_agent_sdk import query, ClaudeAgentOptions

COSTS_LOG = os.environ.get("SDK_COSTS_LOG") or os.path.expanduser("~/.claude/sdk-costs.log")


HEADLESS_NOTE = (
    "This session runs headless through the Agent SDK. Nobody can answer you, and the run "
    "ends the moment you end your turn: anything still running in the background then "
    "(subagents, background shell commands) is stopped and its result lost, and no "
    "notification will ever wake you. So start every agent in the foreground "
    "(run_in_background false), several at once in one message when they are independent, "
    "and never end your turn to wait for something. End your turn only when your work "
    "is finished or you have written down where it stopped."
)


def main() -> None:
    p = argparse.ArgumentParser()
    p.add_argument("--cwd", required=True)
    p.add_argument("--budget", type=float, required=True, help="hard cap in USD for this run")
    p.add_argument("--model", default=None)
    p.add_argument("--effort", default=None)
    p.add_argument("--label", default="")
    p.add_argument("--settings", default=None, help="one more settings file to load")
    p.add_argument("--read-dir", action="append", default=[], help="a folder the session may read without asking")
    p.add_argument("--roadmap", default=None, help="a relay's roadmap.md: a budget stop is noted in its Log")
    g = p.add_mutually_exclusive_group(required=True)
    g.add_argument("--prompt")
    g.add_argument("--prompt-file")
    a = p.parse_args()
    if a.budget <= 0:
        sys.exit("The budget must be more than 0.")
    prompt = a.prompt if a.prompt is not None else open(a.prompt_file).read()

    os.environ.pop("ANTHROPIC_API_KEY", None)

    kw = {}
    if a.model:
        kw["model"] = a.model
    if a.effort:
        kw["effort"] = a.effort
    settings = json.load(open(a.settings)) if a.settings else {}
    settings["apiKeyHelper"] = "/usr/bin/security find-generic-password -s example-keychain-service -w"
    if a.read_dir:
        allow = settings.setdefault("permissions", {}).setdefault("allow", [])
        # "//" starts an absolute path in a permission rule.
        allow += [f"Read(/{os.path.abspath(d)}/**)" for d in a.read_dir]
    kw["settings"] = json.dumps(settings)
    opts = ClaudeAgentOptions(
        cwd=os.path.abspath(a.cwd),
        permission_mode="auto", setting_sources=["user", "project", "local"],
        max_budget_usd=a.budget,
        # One message can be big: a session reading a page picture gets it back
        # as one line. The default 1 MB cap once ended a run
        # mid-way ("JSON message exceeded maximum buffer size").
        max_buffer_size=64 * 1024 * 1024,
        # Claude Code's own system prompt (the SDK's default is a bare one), plus
        # the one rule a run like this needs: it ends when the session ends its
        # turn, so background agents still working then are lost (a reviewer
        # was once cut off mid-read).
        system_prompt={"type": "preset", "preset": "claude_code", "append": HEADLESS_NOTE},
        **kw,
    )
    print(f"{a.label or os.path.basename(a.cwd)}: Agent SDK run on the API key, budget ${a.budget:g}.", flush=True)
    asyncio.run(run(prompt, opts, a))


async def run(prompt: str, opts: ClaudeAgentOptions, a) -> None:
    start = datetime.datetime.now()
    cost, outcome = None, "ended without a result"
    try:
        async for m in query(prompt=prompt, options=opts):
            kind = type(m).__name__
            if kind == "AssistantMessage":
                for b in m.content:
                    bk = type(b).__name__
                    if bk == "TextBlock" and b.text.strip():
                        print(f"\n{b.text.strip()}\n", flush=True)
                    elif bk == "ToolUseBlock":
                        arg = b.input.get("description") or b.input.get("command") or b.input.get("file_path") or b.input.get("prompt", "")
                        print(f"  > {b.name}: {str(arg)[:140]}", flush=True)
            elif kind == "ResultMessage":
                cost = getattr(m, "total_cost_usd", None)
                outcome = getattr(m, "subtype", "result")
    except Exception as e:  # report, don't hide
        outcome = f"error: {e}"
        print(outcome, file=sys.stderr, flush=True)
    mins = (datetime.datetime.now() - start).total_seconds() / 60
    label = a.label or os.path.basename(a.cwd)
    line = f"{start:%Y-%m-%d %H:%M} {label} cost_usd={cost} minutes={mins:.0f} outcome={outcome}"
    print("\n" + line, flush=True)
    with open(COSTS_LOG, "a") as f:
        f.write(line + "\n")
    if outcome == "error_max_budget_usd":
        print(f"Stopped: the budget of ${a.budget:g} is spent.", flush=True)
        if a.roadmap:
            note_budget_stop(a, start, cost)


def note_budget_stop(a, start, cost) -> None:
    """Adds an entry at the end of the roadmap (its Log is the last section)."""
    now = datetime.datetime.now()
    entry = (
        f"\n### {a.label or 'A lead'}: stopped at its budget ({now:%Y-%m-%d %H:%M})\n\n"
        f"Written by sdk-run.py, not by the lead: the lead's Agent SDK budget of ${a.budget:g} "
        f"(the Controls' Lead budget) ran out and the run stopped mid-work, started {start:%H:%M}, "
        f"cost ${cost if cost is not None else '?'}. Its batch is still marked running; what it "
        f"finished is in the files and git. The next start carries on from there.\n"
    )
    try:
        with open(a.roadmap, "a") as f:
            f.write(entry)
    except OSError as e:
        print(f"Couldn't write the budget stop into {a.roadmap}: {e}", file=sys.stderr, flush=True)


if __name__ == "__main__":
    main()
```

### `start-lead.sh`

Starts a fresh Claude Code session to lead the next batch of a project's roadmap, puts finished lead panes away, and with options says what it would start or where the relay stands.

**Needs to run:** Rex, uv, jq, git, and macOS.

````
#!/bin/zsh
# Starts a fresh Claude Code session to lead the next batch of a project's
# roadmap.md: the first batch marked running (left part way by a session that
# was stopped) or queued. A batch marked "queued, low" is neither.
#
# Each batch gets a session of its own, so no session's context grows through
# the whole roadmap (a long-lived lead costs far more each time it wakes).
# The session runs with Remote Control on, named "<Name>: Batch N". It reads
# lead.md in this skill's folder, leads one batch, and runs this script again
# when it ends. With "- Billing: **sdk**" in the Controls it runs through the
# Agent SDK (sdk-run.py) on Orlando's API key instead of his plan, without
# Remote Control, under a budget cap (see "Who pays" below).
#
# In Rex a relay keeps to one tab, named after the session leading now. The
# next lead opens as a pane beside the last one, without taking focus, and
# every finished lead's pane closes by itself once it has been idle for
# fifteen minutes (close-pane-later.sh). So the tab holds one pane most of the
# time, it goes when the relay ends, and nothing is left for Orlando to close
# or to tell apart.
#
#   start-lead.sh [project folder]             start the next session, and put finished ones away (default: the current folder)
#   start-lead.sh [project folder] --dry-run   say what would be started, start nothing
#   start-lead.sh [project folder] --status    say where the relay stands, start nothing
#   start-lead.sh [project folder] --close-me  also put away the pane this was run from, whatever it holds
#                                              (a lead that stops before its batch is done uses it)
set -e
here="${0:A:h}"
skill="${here:h}"
project=""
mode="start"
close_me=0
for arg in "$@"; do
  case "$arg" in
    --dry-run)  mode="dry-run" ;;
    --status)   mode="status" ;;
    --close-me) close_me=1 ;;
    -*)         echo "Unknown option: $arg" >&2; exit 2 ;;
    *)          project="$arg" ;;
  esac
done
project="${${project:-$PWD}:A}"
roadmap="$project/roadmap.md"
[[ -f "$roadmap" ]] || { echo "There is no roadmap.md in $project." >&2; exit 1; }
[[ -f "$skill/lead.md" ]] || { echo "Can't find lead.md in $skill." >&2; exit 1; }

# How a roadmap is read. Only its own lines count, in the template's form:
# the Controls from the first "## Controls" section, and batches from the
# first "## Batches" section, each with its Status on the line right under its
# heading. Text in a code block or a quote is skipped. So a Log entry, a quoted
# line or a pasted example can never switch the relay on, rename it, or queue
# a batch (a security review of the heartbeat found this). Anything
# written another way reads as off or not there. A batch Status, a Stop after
# batch line or a Lead line that can't be read starts nothing and says so,
# rather than being passed over (a review of the setup).

# Shared by every reader below. Windows line endings are dropped. A code block
# opens on a line of three or more backticks or tildes and closes only on a
# line of the same mark, at least as long, with nothing after it, so a block
# shown inside another block can't end it early.
awk_lib='
  { sub(/\r$/, "") }
  function fenceline(line,   m, c, k) {
    if (!match(line, /^ ? ? ?(```+|~~~+)/)) return 0
    m = substr(line, RSTART, RLENGTH); sub(/^ +/, "", m); c = substr(m, 1, 1); k = length(m)
    if (!fence) { fence = 1; fchar = c; flen = k; return 1 }
    if (c == fchar && k >= flen && line ~ /^ ? ? ?(`+|~+)[ \t]*$/) fence = 0
    return 1
  }
'

# The bold value of a Controls line: "- Relay: **on**" gives "on".
control() {
  awk -v key="$1" "$awk_lib"'
    fenceline($0) { next }
    fence { next }
    /^## / { sec = (!seen && $0 ~ /^## Controls([ \t]|$)/); if (sec) seen = 1; next }
    sec && index($0, "- " key ": **") == 1 {
      v = substr($0, length("- " key ": **") + 1)
      if (match(v, /\*\*/)) { print substr(v, 1, RSTART - 1); exit } }
  ' "$roadmap"
}

# Is the relay on? Only when the Controls hold one Relay line, and it is
# exactly "- Relay: **on**" with nothing after it. The heartbeat's watchdog
# reads it the same way, and the two must agree, or a relay that stops is
# never started again. Prints "on", "off", or "unclear" for a Relay line that
# says on in other words, or with more on the line.
relay_state() {
  awk "$awk_lib"'
    fenceline($0) { next }
    fence { next }
    /^## / { sec = (!seen && $0 ~ /^## Controls([ \t]|$)/); if (sec) seen = 1; next }
    sec && tolower($0) ~ /^[ \t]*([-*+][ \t]*)?(\*\*)?[ \t]*relay[ \t]*(\*\*)?[ \t]*:/ {
      lines++
      if ($0 ~ /^- Relay: \*\*on\*\*[ \t]*$/) exact++
      if ($0 ~ /^- Relay: \*\*on\*\*/) says_on++
    }
    END { if (lines == 1 && exact == 1) print "on"; else if (says_on) print "unclear"; else print "off" }
  ' "$roadmap"
}
unclear_relay='roadmap.md'"'"'s Relay line is not exactly "- Relay: **on**": nothing starts. Keep that line to those words alone, once, in the Controls. The heartbeat'"'"'s watchdog reads it the same way.'

# The name sessions carry: the Controls' Name, or the folder's name without
# its date. Kept to plain characters, since it is also searched for by name.
name="$(control Name)"
[[ -n "$name" ]] || name="${${project:t}##[0-9][0-9][0-9][0-9][0-9][0-9][0-9][0-9]-}"
name="${name//[^A-Za-z0-9 _-]/}"
[[ -n "$name" ]] || name="Relay"

# The first batch that is running or queued, in the order of the file. Only
# headings under "## Batches" are batches: a Log entry headed "### Batch 1,
# done" is not one. A running batch was left part way by a session that was
# stopped (a power cut, a usage limit): the new session carries on from what
# is there.
#
# Every numbered batch, one per line: its status word, a tab, its heading. The
# known words are queued, running, done, "queued, low" and "not queued"; any
# other (or no Status line at all) prints "?". So does "**queued** (low)", a
# batch meant to wait whose note is outside the bold. So does a heading whose
# next line opens a code block, is a quote or a "## " heading, or that ends
# the file: the heartbeat's watchdog refuses those too, and the two must agree.
all_batches=$(awk "$awk_lib"'
  name != "" {
    w = "?"
    if ($0 ~ /^Status: \*\*[^*]+\*\*/) {
      w = $0; sub(/^Status: \*\*/, "", w); sub(/\*\*.*/, "", w)
      rest = $0; sub(/^Status: \*\*[^*]+\*\*/, "", rest)
      if (w == "queued" && tolower(rest) ~ /(^|[^a-z])low([^a-z]|$)/) w = "?"
    }
    if (w != "queued" && w != "running" && w != "done" && w != "queued, low" && w != "not queued") w = "?"
    print w "\t" name
    name = ""
  }
  fenceline($0) { next }
  fence { next }
  /^[ \t]*>/ { next }
  /^## / { batches = (!seen && $0 ~ /^## Batches([ \t]|$)/); if (batches) seen = 1; next }
  { name = (batches && $0 ~ /^### Batch [0-9]+/) ? $0 : "" }
  END { if (name != "") print "?\t" name }
' "$roadmap")
found=$(print -r -- "$all_batches" | awk -F'\t' '$1 == "running" || $1 == "queued" { print $1 " " $2; exit }')
unreadable=$(print -r -- "$all_batches" | awk -F'\t' '$1 == "?" && match($2, /Batch [0-9]+/) { print substr($2, RSTART, RLENGTH) }' | paste -sd, - | sed 's/,/, /g')
unreadable_batches="${unreadable:+roadmap.md: the Status line of $unreadable can't be read: nothing starts. Right under the batch's heading it must say Status: **queued**, **running**, **done**, **queued, low** or **not queued**, with any note after the bold.}"

# Stop after batch: "none", a batch number, "absent" when there is no such
# line, or "?" for a line that is there but not in the template's form
# ("- Stop after batch: **2**", a note after the bold is fine).
stop_after=$(awk "$awk_lib"'
  fenceline($0) { next }
  fence { next }
  /^## / { sec = (!seen && $0 ~ /^## Controls([ \t]|$)/); if (sec) seen = 1; next }
  sec && tolower($0) ~ /^[ \t]*([-*+][ \t]*)?(\*\*)?[ \t]*stop[ \t]+after[ \t]+batch/ { n++; line = $0 }
  END {
    if (!n) { print "absent"; exit }
    if (n > 1) { print "?"; exit }
    if (line ~ /^- Stop after batch: \*\*none\*\*/) { print "none"; exit }
    if (line ~ /^- Stop after batch: \*\*[0-9]+\*\*/) { v = line; sub(/^- Stop after batch: \*\*/, "", v); sub(/\*\*.*/, "", v); print v; exit }
    print "?"
  }
' "$roadmap")
unclear_stop='roadmap.md'"'"'s Stop after batch line can'"'"'t be read: nothing starts. Write it as "- Stop after batch: **none**" or with a batch number in the bold, such as "- Stop after batch: **2**".'

# The lead's model and effort, from the Controls' Lead line: default (or no
# line), <model>, <model> <effort>, or <model> at <effort>. Anything else,
# such as an effort alone, is "?": it would start a session that fails at its
# first request.
efforts='^(low|medium|high|xhigh|max)$'
lead_flags=()
lead_says="the saved model and effort"
lead="$(control Lead)"
if [[ -n "$lead" && "$lead" != default ]]; then
  parts=(${=lead})
  [[ "${parts[2]:-}" == at ]] && parts[2]=()
  if (( ${#parts} >= 1 && ${#parts} <= 2 )) && [[ "${parts[1]}" =~ '^[A-Za-z0-9.-]+$' && ! "${parts[1]}" =~ $efforts ]] \
     && [[ ${#parts} == 1 || "${parts[2]}" =~ $efforts ]]; then
    lead_flags+=(--model "${parts[1]}")
    lead_says="${parts[1]}, saved effort"
    if (( ${#parts} == 2 )); then lead_flags+=(--effort "${parts[2]}"); lead_says="${parts[1]} at ${parts[2]}"; fi
  else
    lead_says="?"
  fi
fi
unclear_lead='roadmap.md'"'"'s Lead line can'"'"'t be read: nothing starts. Write **default**, a model (**opus**), or a model and an effort (**opus high**).'

# Who pays: the Controls' Billing line. "plan" (no such line, or **plan**)
# bills Orlando's plan, with Remote Control on. "sdk" runs the lead through
# the Agent SDK (sdk-run.py, beside this script) on his API key, which his
# plan's promotional credit covers, without Remote Control, and stopped at
# the Controls' Lead budget. Claude Code reads the key from the Keychain
# itself, through the apiKeyHelper sdk-run.py sets; this script never reads
# it. Each lead starts the next with this script, so the choice holds until
# the line changes. "api" (an interactive claude with an apiKeyHelper) is
# refused: it is billed as Claude Code, which the credit doesn't cover, and
# it once drained his paid balance until every call was refused.
# "?" for a line that is there but not in a known form, or more than one:
# nothing starts, rather than bill the wrong account.
billing=$(awk "$awk_lib"'
  fenceline($0) { next }
  fence { next }
  /^## / { sec = (!seen && $0 ~ /^## Controls([ \t]|$)/); if (sec) seen = 1; next }
  sec && tolower($0) ~ /^[ \t]*([-*+][ \t]*)?(\*\*)?[ \t]*billing/ { n++; line = $0 }
  END {
    if (!n) { print "plan"; exit }
    if (n > 1) { print "?"; exit }
    if (line ~ /^- Billing: \*\*plan\*\*/) { print "plan"; exit }
    if (line ~ /^- Billing: \*\*sdk\*\*/) { print "sdk"; exit }
    if (line ~ /^- Billing: \*\*api\*\*/) { print "api"; exit }
    print "?"
  }
' "$roadmap")
unclear_billing='roadmap.md'"'"'s Billing line can'"'"'t be read: nothing starts. Write "- Billing: **plan**" or "- Billing: **sdk**", once, or leave the line out for the plan.'
refused_api='roadmap.md says "- Billing: **api**", which is refused: nothing starts. It bills Claude Code to the API key, which the credit does not cover. Write "- Billing: **sdk**" instead.'

# The most an sdk lead may spend, in US dollars: the Controls' "- Lead
# budget: **<amount>**" line, a default with no such line. "?" for a line that is there
# but not a plain number above 0, or more than one: an sdk lead doesn't
# start without a cap it can read.
budget=$(awk "$awk_lib"'
  fenceline($0) { next }
  fence { next }
  /^## / { sec = (!seen && $0 ~ /^## Controls([ \t]|$)/); if (sec) seen = 1; next }
  sec && tolower($0) ~ /^[ \t]*([-*+][ \t]*)?(\*\*)?[ \t]*lead[ \t]+budget/ { n++; line = $0 }
  END {
    if (!n) { print "<default-budget>"; exit }
    if (n > 1) { print "?"; exit }
    if (line ~ /^- Lead budget: \*\*\$?[0-9]+(\.[0-9]+)?\*\*/) { v = line; sub(/^- Lead budget: \*\*\$?/, "", v); sub(/\*\*.*/, "", v); if (v + 0 > 0) { print v; exit } }
    print "?"
  }
' "$roadmap")
unclear_budget='roadmap.md'"'"'s Lead budget line can'"'"'t be read: nothing starts on sdk. Write it as "- Lead budget: **<amount>**" (US dollars, above 0), once, or leave it out for the default.'
case "$billing" in
  sdk)  billing_says="the API key through the Agent SDK, without Remote Control, at most \$$budget a lead" ;;
  plan) billing_says="the plan, with Remote Control" ;;
  api)  billing_says="api, refused (use sdk)" ;;
  *)    billing_says="?" ;;
esac
# How a lead's process is told apart, by its command line. On the plan:
# "claude --remote-control <its name> ...". An older lead on the key: "claude
# --name <its name> ...". On sdk: "uv run <this folder>/sdk-run.py ...
# --label <its name> ...", the uv process being the pane's own program. Each
# is followed by "<Name>: Batch N". Two groups: the batch number is \3.
lead_re='^([^ ]*claude --(remote-control|name) |[^ ]*uv run [^ ]*sdk-run\.py .*--label )'

state="${found%% *}"
batch=$(print -r -- "${found#* }" | sed -E 's/^### (Batch [0-9]+).*/\1/')
number="${batch#Batch }"
sess="$name: $batch"

# This script's own ancestors: a lead that calls it is one of them.
ancestors=()
pid=$$
while [[ -n "$pid" && "$pid" != "1" && "$pid" != "0" ]]; do
  ancestors+=("$pid")
  pid=$(ps -o ppid= -p "$pid" 2>/dev/null | tr -d ' ')
done
# The status word of a batch, by its number: queued, running, done.
status_of() {
  awk -v n="$1" "$awk_lib"'
    fenceline($0) { on = 0; next }
    fence { next }
    /^[ \t]*>/ { on = 0; next }
    /^## / { batches = (!seen && $0 ~ /^## Batches([ \t]|$)/); if (batches) seen = 1; on = 0; next }
    { if (on && $0 ~ /^Status: /) { s = $0; sub(/^Status: \*\*/, "", s); sub(/\*\*.*/, "", s); print s; exit }
      on = (batches && $0 ~ ("^### Batch " n "([^0-9]|$)")) }
  ' "$roadmap"
}
# Is a lead of this relay the one calling? Then which batch is its own, and
# whether that batch is done. A lead whose batch is done has ended its work,
# so its pane is put away below whatever else happens.
caller_batch=""
for a in "${ancestors[@]}"; do
  n=$(ps -o args= -p "$a" 2>/dev/null | sed -n -E "s/${lead_re}$name: Batch ([0-9]+)([^0-9].*)?$/\3/p" | head -1)
  if [[ -n "$n" ]]; then caller_batch="$n"; fi
done
caller_done=0
if [[ -n "$caller_batch" && "$(status_of "$caller_batch")" == done ]]; then caller_done=1; fi
# Other lead sessions of this project. One whose batch is marked done has
# finished and is only an open pane, waiting to be put away: it is not
# leading anything, and doesn't count.
others=()
finished=()
finished_pids=()
for other in $(pgrep -f "${lead_re}$name: Batch [0-9]+" || true); do
  (( ${ancestors[(Ie)$other]} )) && continue
  n=$(ps -o args= -p "$other" 2>/dev/null | sed -n -E "s/${lead_re}$name: Batch ([0-9]+)([^0-9].*)?$/\3/p" | head -1)
  if [[ -n "$n" && "$(status_of "$n")" == done ]]; then
    finished+=("Batch $n")
    finished_pids+=("$other")
  else
    others+=("$other")
  fi
done

rex_bin=$(command -v rex || true)
[[ -n "$rex_bin" ]] || rex_bin="$HOME/Library/Application Support/com.superlogical.rex/RexServer/Versions/Current/Rex Server.app/Contents/MacOS/rex"
# jq (part of macOS) reads what Rex says about its panes. Without it sessions
# still start, but their tab isn't named and stray finished panes aren't found.
jq_bin=$(command -v jq || true)
# How long a finished pane must be idle before it closes. Tests shorten it.
close_after="${RELAY_CLOSE_AFTER:-900}"

# pane_of <process id>: the Rex pane that process runs in. Rex knows each
# pane's program by its process id. Sets REPLY to the pane's block id.
pane_of() {
  REPLY=""
  [[ -x "$rex_bin" && -n "$jq_bin" ]] || return 1
  local s b p
  for s in $("$rex_bin" ls --json --autostart=false 2>/dev/null | "$jq_bin" -r '.sessions[]?.session_id' 2>/dev/null); do
    for b in $("$rex_bin" block ls -s "$s" --json --autostart=false 2>/dev/null | "$jq_bin" -r '.blocks[]?.block_id' 2>/dev/null); do
      p=$("$rex_bin" block call -b "$b" process --autostart=false 2>/dev/null | "$jq_bin" -r '.child.pid // empty' 2>/dev/null) || p=""
      if [[ "$p" == "$1" ]]; then REPLY="$b"; return 0; fi
    done
  done
  return 1
}

# pane_state <process id>: what that lead's pane reports, for --status:
# "working", "idle for 2 h 10 min", or nothing when it can't be read. Read the
# same way close-pane-later.sh reads it. Sets REPLY, and LEAD_IDLE to the idle
# seconds (0 when working or unknown).
pane_state() {
  REPLY="" LEAD_IDLE=0
  pane_of "$1" || return 1
  local st count working last at idle
  st=$("$rex_bin" block call -b "$REPLY" program_status --autostart=false 2>/dev/null) || { REPLY=""; return 1; }
  REPLY=""
  print -r -- "$st" | "$jq_bin" -r '[.records[]?] as $r | "\($r | length) \([$r[] | select(.state == "working")] | length) \([$r[] | .updated_at // empty] | max // "")"' 2>/dev/null | read -r count working last
  [[ "$count" == <-> ]] && (( count > 0 )) || return 1
  if (( working > 0 )); then REPLY="working"; return 0; fi
  zmodload zsh/datetime
  [[ -n "$last" ]] && strftime -r -s at '%Y-%m-%dT%H:%M:%S' "${last[1,19]}" 2>/dev/null || return 1
  idle=$(( EPOCHSECONDS - at ))
  (( idle >= 0 )) || return 1
  LEAD_IDLE=$idle
  if (( idle >= 3600 )); then REPLY="idle for $(( idle / 3600 )) h $(( idle % 3600 / 60 )) min"
  else REPLY="idle for $(( idle / 60 )) min"; fi
  return 0
}

if [[ "$mode" == status ]]; then
  echo "$name ($project)"
  awk "$awk_lib"'
    fenceline($0) { next }
    fence { next }
    /^## / { sec = (!seen && $0 ~ /^## Controls([ \t]|$)/); if (sec) seen = 1; next }
    sec && /^- (Relay|Stop after batch|Agents|Lead):/ { s = $0; sub(/^- /, "  ", s); gsub(/\*\*/, "", s); print s }
  ' "$roadmap"
  if [[ "$billing" == "?" ]]; then echo "  Billing: can't be read"; else echo "  Billing: $billing_says"; fi
  echo "Batches:"
  awk "$awk_lib"'
    fenceline($0) { name = ""; next }
    fence { next }
    /^[ \t]*>/ { name = ""; next }
    /^## / { batches = (!seen && $0 ~ /^## Batches([ \t]|$)/); if (batches) seen = 1; name = ""; next }
    { if (name != "" && $0 ~ /^Status: /) { s = $0; gsub(/\*\*/, "", s); sub(/^Status: /, "", s); print "  " name ": " s }
      name = (batches && $0 ~ /^### Batch [0-9]+/) ? substr($0, 5) : "" }
  ' "$roadmap"
  if (( ${#others} )); then
    # Each live lead, with what its pane reports. A lead stopped by a usage
    # limit in the middle of a turn stays alive and idle, and blocks every
    # restart until it is told to carry on or its pane is closed.
    running=() long_idle=0
    for o in "${others[@]}"; do
      r=$(ps -o args= -p "$o" 2>/dev/null | sed -n -E "s/${lead_re}($name: Batch [0-9]+)([^0-9].*)?$/\3/p" | head -1)
      [[ -n "$r" ]] || r="process $o"
      if pane_state "$o" && [[ -n "$REPLY" ]]; then r="$r ($REPLY)"; (( LEAD_IDLE >= 3600 )) && long_idle=1; fi
      running+=("$r")
    done
    echo "A lead session is running: ${(j:, :)running}"
    if (( long_idle )); then echo "A lead idle this long may have stopped at a usage limit, and blocks every restart. See \"After a crash, a power cut or a usage limit\" in the relay skill."; fi
  else
    echo "No lead session is running."
  fi
  if (( ${#finished} )); then echo "Finished, with its pane still open: ${(j:, :)finished}"; fi
  if [[ -n "$found" ]]; then echo "Next to lead: $batch ($state)"; else echo "Nothing is running or queued."; fi
  if [[ "$(relay_state)" == unclear ]]; then echo "$unclear_relay"; fi
  [[ -n "$unreadable_batches" ]] && echo "$unreadable_batches"
  [[ "$stop_after" == "?" ]] && echo "$unclear_stop"
  if [[ "$lead_says" == "?" ]]; then echo "$unclear_lead"; else echo "A new lead starts on: $lead_says."; fi
  [[ "$billing" == "?" ]] && echo "$unclear_billing"
  [[ "$billing" == api ]] && echo "$refused_api"
  [[ "$billing" == sdk && "$budget" == "?" ]] && echo "$unclear_budget"
  if pgrep -f 'memory-fuse\.sh watch$' >/dev/null 2>&1; then
    echo "The memory fuse is watching."
  else
    echo "The memory fuse is not watching. It starts with the next lead."
  fi
  exit 0
fi

# close_later <block id>: closes that pane once its session has been idle
# long enough. The timer runs on its own, in no pane, one to a pane.
close_later() {
  if pgrep -f "close-pane-later.sh $1 " >/dev/null 2>&1; then return 0; fi
  ( "$here/close-pane-later.sh" "$1" "$close_after" </dev/null >/dev/null 2>&1 & )
}

# Finished leads whose panes are still open (a lead that ended without
# running this script, or whose timer was lost) are put away too.
sweep_finished() {
  local p
  for p in "${finished_pids[@]}"; do
    if pane_of "$p" && [[ "$REPLY" != "$REX_BLOCK" ]]; then close_later "$REPLY"; fi
  done
  return 0
}

# The pane this was run from, when a lead whose batch is done is the one
# calling, or --close-me was given.
close_caller() {
  [[ "$mode" == start && -n "$REX_BLOCK" && -x "$rex_bin" ]] || return 0
  (( close_me || caller_done )) || return 0
  close_later "$REX_BLOCK"
  echo "This session's pane closes by itself once it has been idle for $(( (close_after + 59) / 60 )) minutes. Write your last message to Orlando now, and do nothing else afterwards."
}

# What a real run ends with, whether or not anything was started.
put_away() {
  [[ "$mode" == start ]] || return 0
  sweep_finished
  close_caller
}

if [[ -n "$unreadable_batches" ]]; then
  echo "$unreadable_batches"
  put_away
  exit 0
fi
if [[ -z "$found" ]]; then
  echo "No batch is running or queued in roadmap.md: nothing to start."
  put_away
  exit 0
fi
case "$(relay_state)" in
  on) ;;
  unclear)
    echo "$unclear_relay"
    put_away
    exit 0 ;;
  *)
    echo "roadmap.md says the relay is off: nothing started."
    put_away
    exit 0 ;;
esac
if [[ "$stop_after" == "?" ]]; then
  echo "$unclear_stop"
  put_away
  exit 0
fi
if [[ "$state" == queued && "$stop_after" == <-> && "$number" -gt "$stop_after" ]]; then
  echo "roadmap.md says to stop after batch $stop_after: nothing started."
  put_away
  exit 0
fi
# Never two lead sessions for one project at once. The session that has just
# finished the batch before is still running when it calls this: it is one of
# this script's ancestors, and doesn't count.
if (( ${#others} )); then
  echo "Another lead session for $name is already running (process ${others[1]}): nothing started."
  close_caller
  exit 0
fi
# Nor a second session for a batch whose own lead is the one calling: it
# marks its batch done before it starts the next. With --close-me it is
# stopping part way, and only wants its pane put away.
if [[ "$state" == running && -n "$caller_batch" && "$caller_batch" == "$number" ]]; then
  if (( close_me )); then
    echo "$batch is marked running and this session is leading it: nothing started. The next start carries on from what is in the files."
    close_caller
  else
    echo "$batch is marked running and this session is leading it: nothing started. Mark it done in roadmap.md first."
  fi
  exit 0
fi

if [[ "$lead_says" == "?" ]]; then
  echo "$unclear_lead"
  put_away
  exit 0
fi
if [[ "$billing" == "?" ]]; then
  echo "$unclear_billing"
  put_away
  exit 0
fi
if [[ "$billing" == api ]]; then
  echo "$refused_api"
  put_away
  exit 0
fi
if [[ "$billing" == sdk && "$budget" == "?" ]]; then
  echo "$unclear_budget"
  put_away
  exit 0
fi

# RELAY_TEST_CLAUDE puts a stand-in in claude's place, for this script's own tests.
claude_bin="${RELAY_TEST_CLAUDE:-$HOME/.local/bin/claude}"
[[ -x "$claude_bin" ]] || claude_bin=$(command -v claude || true)
[[ -n "$claude_bin" ]] || { echo "Can't find the claude command." >&2; exit 1; }
[[ -x "$rex_bin" ]] || { echo "Can't find rex: the relay starts its sessions in Rex panes." >&2; exit 1; }

# The prompt is one plain line, so no shell on the way can mangle it.
prompt="Read $skill/lead.md and do what it says."
carrying=""
[[ "$state" == running ]] && carrying=" It carries on from where the stopped session left $batch."
opening=("$rex_bin" run --json --focus=false --label "$sess" -c "$project")
# lead-settings.json turns a permission question into a "no", so a lead never
# waits all night on one. Leads only: his own
# sessions still ask.
session_cmd=(-- "$claude_bin" --remote-control "$sess" --permission-mode auto --settings "$skill/lead-settings.json" "${lead_flags[@]}" "$prompt")
# On sdk: the same first message, model, effort, settings file and
# permission mode, through sdk-run.py, which may read this skill's folder
# (lead.md, the briefs) without asking, and loads the user's, project's and
# local settings too (their deny rules hold) and stops at the budget. The
# lead's agents run in its process, so they bill the key too. A lead stopped
# by its budget gets an entry in roadmap.md's Log from sdk-run.py.
# RELAY_TEST_UV puts a stand-in in uv's place, for this script's own tests.
if [[ "$billing" == sdk ]]; then
  uv_bin="${RELAY_TEST_UV:-$(command -v uv || true)}"
  [[ -n "$uv_bin" ]] || uv_bin="$HOME/.local/bin/uv"
  [[ -x "$uv_bin" ]] || { echo "Can't find uv: an sdk lead runs with uv run." >&2; exit 1; }
  session_cmd=(-- "$uv_bin" run "$here/sdk-run.py" --cwd "$project" --budget "$budget" --settings "$skill/lead-settings.json" --read-dir "$skill" --roadmap "$roadmap" "${lead_flags[@]}" --label "$sess" --prompt "$prompt")
fi

if [[ "$mode" == dry-run ]]; then
  echo "Would start: $sess.$carrying"
  echo "On: $lead_says."
  [[ "$billing" == sdk ]] && echo "Billing: $billing_says."
  printf '%q ' "${opening[@]}" ${REX_SESSION:+-s} ${REX_SESSION:+"$REX_SESSION"} "${session_cmd[@]}"; echo
  exit 0
fi

# Where the new pane goes. Beside the lead that is handing over (the caller),
# or beside a finished lead's pane that is still open, so the relay keeps its
# tab. With neither, a new tab: in this Rex session if there is one, in Rex's
# default place otherwise.
anchor=""
if [[ -n "$caller_batch" && -n "$REX_BLOCK" ]]; then
  anchor="$REX_BLOCK"
else
  for p in "${finished_pids[@]}"; do
    if pane_of "$p"; then anchor="$REPLY"; break; fi
  done
fi
out=""
started=0
if [[ -n "$anchor" ]]; then
  if out=$("${opening[@]}" --split=right -b "$anchor" "${session_cmd[@]}" 2>&1); then started=1; fi
fi
if (( ! started )); then
  if [[ -n "$REX_SESSION" ]] && out=$("${opening[@]}" -s "$REX_SESSION" "${session_cmd[@]}" 2>&1); then
    :
  elif out=$("${opening[@]}" "${session_cmd[@]}" 2>&1); then
    :
  else
    echo "Rex could not start the session: $out" >&2
    exit 1
  fi
fi
# The new pane's id. A split's answer also names the pane it was split from
# (its anchor), which is not the one wanted.
block=""
if [[ -n "$jq_bin" ]]; then
  block=$(print -r -- "$out" | "$jq_bin" -r '.block_ids[0] // empty' 2>/dev/null) || block=""
fi
if [[ -z "$block" ]]; then
  block=$(print -r -- "$out" | grep -v '"anchor_id"' | sed -n -E 's/.*"(block:[^"]+)".*/\1/p' | head -1)
fi

# Name the relay's tab after the session leading now, so Orlando can tell his
# tabs apart. A name he gave the tab himself is left alone.
name_tab() {
  [[ -n "$jq_bin" && -n "$block" ]] || return 0
  local info s w label
  info=$("$rex_bin" block inspect "$block" --json 2>/dev/null) || return 0
  s=$(print -r -- "$info" | "$jq_bin" -r '.session_id // empty' 2>/dev/null) || return 0
  w=$(print -r -- "$info" | "$jq_bin" -r '.block.window_id // empty' 2>/dev/null) || return 0
  [[ -n "$s" && -n "$w" ]] || return 0
  label=$("$rex_bin" window ls -s "$s" --json 2>/dev/null | "$jq_bin" -r --arg w "$w" '.windows[]? | select(.window_id == $w) | .label // empty' 2>/dev/null) || label=""
  if [[ -z "$label" || "$label" =~ '^Window [0-9a-f]+$' || "$label" == "$name: Batch "* ]]; then
    "$rex_bin" window rename "$w" "$sess" -s "$s" >/dev/null 2>&1 || true
  fi
  return 0
}
name_tab

# Did it come up? Its process should be there within a few seconds.
up=0
for i in {1..20}; do
  for p in $(pgrep -f "${lead_re}$sess( |\$)" || true); do
    (( ${ancestors[(Ie)$p]} )) || up=1
  done
  (( up )) && break
  sleep 1
done
if (( ! up )); then
  echo "The session for $batch did not come up. Look at its pane: rex capture -b $block" >&2
  exit 1
fi
if [[ "$billing" == sdk ]]; then
  echo "Started a fresh session for $batch through the Agent SDK, billed to the API key, at most \$$budget. It is in this relay's tab in Rex as \"$sess\", and not in the Claude app: Remote Control needs the plan. Its cost goes to ~/.claude/sdk-costs.log.$carrying"
else
  echo "Started a fresh session for $batch. It is in this relay's tab in Rex and in the Claude app, both as \"$sess\".$carrying"
fi
echo "Read its pane with: rex capture -b $block"

# The memory fuse: one for the whole Mac while any relay lead runs. It stops a
# single runaway process that a lead or its agents started, before macOS runs
# out of memory. It runs on its own, in no pane, and ends by itself once no
# lead is left. A second one ends at once. A run with a stand-in for claude
# (this script's own tests) starts none.
if [[ -z "${RELAY_TEST_CLAUDE:-}" && -x "$here/memory-fuse.sh" ]]; then
  ( "$here/memory-fuse.sh" watch </dev/null >/dev/null 2>&1 & )
fi

# Put the finished sessions away, so old ones don't pile up for Orlando to
# close by hand. What each did is in roadmap.md's Log and in git.
put_away
````

### `watch-relay.zsh`

Watches one relay from another session and exits with an "EVENT:" line when a batch finishes, no lead is running, the relay's sessions go quiet for 30 minutes, or a time limit passes. It costs nothing while it waits.

**Needs to run:** macOS, and the relay's `start-lead.sh` script.

```
#!/bin/zsh
# Watches one relay and exits when something needs a look. It costs nothing while it waits.
# Usage: watch-relay.zsh <project folder> [minutes before it stops waiting]
# It prints a line starting "EVENT:" and exits when:
#   - a batch has been marked done since it started watching
#   - no lead session is running
#   - the relay's sessions have written nothing for 30 minutes
#   - the time limit passes
emulate -L zsh
repo=${1:?project folder}
limit_min=${2:-300}
script=$HOME/.claude/skills/relay/scripts/start-lead.sh
projects=$HOME/.claude/projects
pattern="*${repo:h:t}-${repo:t}*"

relay_status() { "$script" "$repo" --status 2>&1 }
count_done() { print -r -- "$1" | grep -c ': done' }
session_dirs() { find "$projects" -maxdepth 1 -type d -name "$pattern" }

start=$(date +%s)
first=$(relay_status)
done_start=$(count_done "$first")
watch_quiet=yes
[[ -z "$(session_dirs)" ]] && watch_quiet=no
print -r -- "Watching since $(date '+%a %H:%M'). Batches done at start: $done_start. Quiet check: $watch_quiet."

while true; do
  sleep 300
  out=$(relay_status)
  stamp=$(date '+%a %H:%M')
  if (( $(count_done "$out") > done_start )); then
    print -r -- "EVENT: a batch finished ($stamp)"; print -r -- "$out"; exit 0
  fi
  if ! print -r -- "$out" | grep -q 'A lead session is running'; then
    print -r -- "EVENT: no lead session is running ($stamp)"; print -r -- "$out"; exit 0
  fi
  if [[ $watch_quiet == yes ]]; then
    recent=$(session_dirs | while read -r d; do find "$d" -type f -mmin -30 -print -quit; done 2>&1 | head -1)
    if [[ -z "$recent" ]]; then
      print -r -- "EVENT: the relay's sessions have written nothing for 30 minutes ($stamp)"; print -r -- "$out"; exit 0
    fi
  fi
  if (( ( $(date +%s) - start ) / 60 >= limit_min )); then
    print -r -- "EVENT: time limit of $limit_min minutes ($stamp)"; print -r -- "$out"; exit 0
  fi
done
```

Previous: [Heartbeat](https://oneillo.com/ai-setup/skills/heartbeat/).
Next: [Rex](https://oneillo.com/ai-setup/skills/rex/).

[Back to Skills](https://oneillo.com/ai-setup/skills/)

[Borrowed something? Tell me how it went](https://www.linkedin.com/in/oneillo/)

Text: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Scripts: [MIT](https://opensource.org/license/mit).
