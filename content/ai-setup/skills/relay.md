---
title: "Relay: a Claude Code skill that runs a roadmap while you're away"
date: 2026-11-22
lastmod: 2026-11-23
summary: "AI-written: Runs a project's roadmap unattended as a relay of fresh Claude Code sessions, each leading one batch with parallel agents and handing off to the next."
heading: "Relay"
linkTitle: "Relay"
author: "Orlando O'Neill"
images: ["/ai-setup/cards/skills/relay.png"]
ShowReadingTime: false
ShowShareButtons: false
disableShare: true
ShowPostNavLinks: false
ShowBreadCrumbs: true
ShowWordCount: false
hideMeta: true
madeBy: "setup-share"
---

<div class="post-description">Runs a project's roadmap unattended as a relay of fresh Claude Code sessions, each leading one batch with parallel agents and handing off to the next.</div>

> **Written by AI.** This page was generated from Orlando's own setup by an automated run, and checked for private details before it was published. <a href="/ai-setup/#how-this-section-is-made">How this section is made</a>. Last updated 2026-11-23.

- **What it is:** Relay is a Claude Code skill that runs a project's roadmap while its owner is away, with a fresh session leading each batch.
- **How it starts:** You type <code>&#47;relay</code>. Claude can also pick it up when it fits.
- **Where the file goes:** <code>&#126;&#47;&#46;claude&#47;skills&#47;relay&#47;SKILL&#46;md</code>, its other files beside it
- **Needs:** Rex, uv, jq, git, and macOS
- **To make it yours:** Put your own values where you see <code>&#60;amount&#62;</code> and <code>&#60;default&#45;budget&#62;</code>.
  Taken out for privacy: 15 dates and incident specifics, 7 amounts, 1 keychain service name, and 1 private project name

**Works with:** <a href="/ai-setup/apps/#corillo">Corillo</a>, <a href="/ai-setup/skills/heartbeat/">Heartbeat</a>, <a href="/ai-setup/apps/#pregon">Pregón</a>, and <a href="/ai-setup/skills/rex/">Rex</a>.

## The skill file (<code>SKILL&#46;md</code>) {#skill-md}

<a href="#skill-md">Link to this file</a>

The main guide for the relay skill. It explains what a relay is, when it fits, how to set one up, and how to start, check, pause, stop, and recover it.

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere">---
name: relay
description: Run a plan as an unattended relay of fresh Claude Code sessions. Each session leads one batch of the project&#x27;s roadmap.md with parallel agents, checks it, delivers it, writes it up, then starts the next session in a new Rex pane with Remote Control on. Use this whenever Orlando wants a roadmap or a long list of work run while he is away, asks to set up &quot;the loop&quot;, &quot;the relay&quot; or &quot;the cycle&quot; in a project, wants the automated loop that built Corillo used somewhere else, or asks to start, check, pause, stop or restart one, including after a crash, a power cut or a usage limit.
---

# Relay

A relay works through a project&#x27;s `roadmap.md` without Orlando at the computer. The roadmap is cut into batches. A fresh Claude Code session leads one batch: it briefs up to three builders, brings their work together, runs the project&#x27;s checks, has a fresh reviewer read the result, delivers it, writes it up, and starts the next session. Then it ends.

Why it is built this way:

- **A fresh session per batch.** A lead that lives through the whole roadmap costs more every time it wakes, and gets worse as its context fills. So the state lives in `roadmap.md`, `context.md` and git, never in a session&#x27;s memory.
- **Nothing waits for Orlando.** Every decision that is his sits in the roadmap with a recommendation, and a batch goes by the recommendation unless he has answered. The calls a lead makes are written in the Log for him to look at afterwards.
- **It can be stopped and started anywhere.** A batch left &quot;running&quot; by a power cut or a usage limit is picked up from what is in the files.
- **He can watch and steer.** Every lead runs with Remote Control on, named &quot;&lt;Project&gt;: Batch N&quot;, and the Controls at the top of the roadmap are his to change at any time. A relay on `sdk` billing runs without Remote Control (see &quot;Who pays&quot;).
- **It tidies up after itself.** In Rex a relay keeps to one tab, named after the session leading now (&quot;Heartbeat: Batch 3&quot;). Each new lead opens as a pane beside the last, and a finished lead&#x27;s pane closes by itself once it has been idle for 15 minutes, the last lead&#x27;s included. So the tab holds one pane most of the time and goes when the relay ends. What a session did is in the roadmap&#x27;s Log and in git, not in an open pane.

`&lt;skill&gt;` below is this file&#x27;s folder.

| File | For |
|---|---|
| `scripts/start-lead.sh` | Starts the next lead session and puts finished ones away, or says where the relay stands |
| `scripts/close-pane-later.sh` | Closes a finished lead&#x27;s pane once it is idle. `start-lead.sh` starts it. With `--say` it only says what it would do |
| `scripts/memory-fuse.sh` | Stops a single runaway process that a lead or its agents started, before macOS runs out of memory. `start-lead.sh` starts it, and it ends by itself. With `say` it only says what it sees |
| `scripts/watch-relay.zsh` | Watches one relay from another session and exits with an &quot;EVENT:&quot; line when a batch finishes, no lead is running, or the relay goes quiet for 30 minutes. Costs nothing while it waits |
| `tests/memory-fuse.test.zsh` | Proves the fuse on made-up processes in about 30 seconds. Run it after changing the fuse, or after a macOS update |
| `tests/start-lead.test.zsh` | Proves how `start-lead.sh` reads a roadmap, on made-up roadmaps: what it starts, and that text in a Log can&#x27;t steer it. Starts nothing. Run it after changing that script |
| `lead-settings.json` | Settings every lead starts with: a permission question becomes a &quot;no&quot;, so a lead never waits all night on one. Leads only, not Orlando&#x27;s own sessions |
| `scripts/sdk-run.py` | Runs one session through the Agent SDK on Orlando&#x27;s API key, with a budget cap. `start-lead.sh` uses it when the Controls say `- Billing: **sdk**`; it works by hand too (`uv run &lt;skill&gt;/scripts/sdk-run.py --help`) |
| `lead.md` | What a lead session does. A lead reads it; you don&#x27;t need to unless you are changing how leads work |
| `builder-brief.md`, `reviewer-brief.md` | What every builder and reviewer is told |
| `roadmap-template.md` | The form of `roadmap.md` |

## Does a relay fit?

Say so before setting one up if it doesn&#x27;t. A relay fits work that:

- can be cut into batches of two or three items that don&#x27;t depend on each other;
- has a check that says &quot;done&quot; without Orlando: tests, a build, a fresh reader holding the result against what was promised;
- is worth the usage. Each batch is a lead, up to three builders and a reviewer.

It doesn&#x27;t fit work that needs his judgment at each step (writing in his voice, a decision with real stakes), or anything one session can finish. For &quot;check on something every few minutes&quot;, `/loop` is the tool.

## Set one up

1. **Agree the plan with Orlando first.** The roadmap is his: what gets built, in what order, and what is left out. If no plan exists, make one with him (one question per message), or from reviews he has asked for. Never queue work he hasn&#x27;t agreed to.
2. **Write `roadmap.md`** at the project&#x27;s root from `&lt;skill&gt;/roadmap-template.md`. What makes a roadmap run well unattended:
   - Each item says what the person gets, how big it is, and which files are its own, so two builders never edit the same file.
   - Items in one batch are independent. Work that builds on other work goes in a later batch.
   - Every decision that is Orlando&#x27;s is in the table with a recommendation. Anything that truly needs him (an account, a purchase, a judgment only he can make) goes under &quot;Later&quot;, or in a batch marked `Status: **queued, low**`, which the relay skips.
   - &quot;How this project runs&quot; is filled in from the project itself: find the real command for the checks and run it once, so the first lead doesn&#x27;t discover that it fails. Write down the delivery step and whether Orlando has agreed it may be done without asking.
   - **Keep the template&#x27;s form for what the scripts read.** The relay is on only when the `## Controls` section has one Relay line and it is exactly `- Relay: **on**`, with nothing after it (a note goes on a line of its own). A batch&#x27;s `Status:` is the line right under its `### Batch N` heading, in the `## Batches` section. Anything written another way reads as off or not there, and nothing starts. That is on purpose: a Log entry, a quoted line or a pasted example must never be able to switch a relay on or queue a batch (a security review of the heartbeat found this). The heartbeat&#x27;s watchdog reads a roadmap the same way.
3. **Make sure the project can carry it.** Code needs its own git repo for builders to work side by side in worktrees (follow the workspace&#x27;s rules for a repo inside `~/ai`). The project needs a `CLAUDE.md` and a `context.md`: every lead and builder starts from them. The folder must be one Claude Code has been used in before, or the new session stops at a question about trusting it. A new git repo gets that question even inside `~/ai`: trust goes by the repo&#x27;s root. Who answers it depends on who made the folder:
   - **A folder you made yourself inside `~/ai`, in this same sitting** (your own `git init`, and nothing cloned, downloaded or unpacked in it): answer it yourself. Orlando&#x27;s rule: &quot;~/ai is my agent workspace. You all have permission to do anything you want in that folder including anything contained within it.&quot; Open a plain `claude` there in its own Rex pane without focus (`rex run --json --focus=false --label &quot;&lt;Name&gt;: trust this folder&quot; -c &lt;project folder&gt; -s &quot;$REX_SESSION&quot; -- ~/.local/bin/claude`), read the pane (`rex capture -b &lt;block id&gt;`) and go on only if it shows the question. Send the Down arrow (`rex send -b &lt;block id&gt; $&#x27;\e[B&#x27;`), read the pane again to see &quot;Yes, I trust this folder&quot; picked, send Return (`$&#x27;\r&#x27;`), and check that Claude Code recorded it: `jq &#x27;.projects[&quot;&lt;full path&gt;&quot;].hasTrustDialogAccepted&#x27; ~/.claude.json` says `true`. Then end that session (`/exit`, Return) and say in the project&#x27;s log that you answered it. Never write to `~/.claude.json` yourself.
   - **Any other folder** (a clone, a download, something another program made, anything outside `~/ai`): only Orlando answers, at the Mac&#x27;s keyboard, not from his phone. Open the pane the same way, name its tab (`rex window rename &lt;window id&gt; &quot;&lt;Name&gt;: trust this folder&quot; -s &lt;session&gt;`), and put on his list: that tab, Down arrow once, Return.
4. **Dry run:** `&lt;skill&gt;/scripts/start-lead.sh &lt;project folder&gt; --dry-run` says which batch would start and under what name.
5. **Tell Orlando what will happen and what it will cost** (how many batches, how many agents in each), then start it on his word.

## Start, check, pause, stop

- **Start, or start again:** `&lt;skill&gt;/scripts/start-lead.sh &lt;project folder&gt;`. It starts the first batch that is running or queued, never a second lead for the same project, and nothing if the Controls say the relay is off. Then read the new pane once with the `rex capture` command it prints, to see that the session began work.
- **Check:** `&lt;skill&gt;/scripts/start-lead.sh &lt;project folder&gt; --status` lists the Controls, each batch&#x27;s status, whether a lead is running and what its pane reports (&quot;working&quot;, or &quot;idle for 2 h 10 min&quot;), and what a new lead would start on. A line it can&#x27;t read (a batch&#x27;s Status, Stop after batch, Lead, Billing, and on sdk Lead budget) is named there, and nothing starts until it is fixed. The roadmap&#x27;s Log has what each finished batch did. To see what a lead is doing right now, read its pane with `rex capture`, or tell Orlando the session&#x27;s name so he can open it in the Claude app.
- **Pause after a batch:** set &quot;Stop after batch&quot; in the Controls to that batch&#x27;s number. To stop when the current batch ends, set Relay to **off**. A lead reads the Controls again just before it hands off.
- **Stop now:** tell the live lead to stop cleanly (type into its pane with `rex send`, or Orlando tells it over Remote Control). It commits what is finished, writes where things stand, and ends. Closing its pane instead leaves the batch &quot;running&quot; with its work in the files, which the next start also picks up, but any agents in flight are lost. If the heartbeat&#x27;s relay watchdog watches the project (its job file lists the folder), a relay that is on is started again within about an hour of its lead going away, so set Relay to **off** first when the stop is meant to last.
- **Add work:** only what Orlando has agreed. His bugs and ideas for an app wait in the `feedback.md` at its root; a new batch starts from the open ones there and marks each one it builds. If a lead is running, tell it and let it write the batches in at its write-up, so two sessions don&#x27;t edit the roadmap at once. Otherwise add them to `roadmap.md` in the template&#x27;s form.
- **Telling a live lead something:** read its pane first (`rex capture -b &lt;pane&gt;`), and never type while it shows a permission question. Then `rex send -b &lt;pane&gt; &#x27;&lt;one short line&gt;&#x27;` and `rex send-key -b &lt;pane&gt; enter`. Say which session is writing and that Orlando did not type it. Anything longer goes in a file the lead can read, and the line points at it: a note of about 2,700 characters typed this way once arrived as its last 30 characters, and a line of 220 arrived whole. Read the pane again afterwards to see that the lead answered what you sent.

A project that already has its own launcher keeps using it until Orlando asks to move it over to this skill. Corillo does (`scripts/next-orchestrator.sh`): its leads follow the project&#x27;s own prompt under their own name, so this script would not see them and could start a second lead.

Each lead keeps the computer awake with its own `caffeinate -ims` (the display may sleep). Two relays can run at once in different projects, but they share Orlando&#x27;s usage and the computer, so start a second only when he asks.

One memory fuse watches every relay on the Mac. A single process that a lead or its agents started is stopped once it passes a quarter of the Mac&#x27;s memory (4 GB of 16). The lead gets one line in its pane, and a note goes in `~/.claude/relay-fuse.log`. It exists because a test once fed a very long answer to a shell line whose memory grows with the square of its input: one process asked for many times the Mac&#x27;s memory within seconds, and macOS raised its out-of-memory alarm over and over while most memory was free. What the fuse does not limit: many middling processes at once, the processor, and anything no relay lead started (this session, a heartbeat job).

## Who pays

The Controls&#x27; Billing line picks one of three modes. Change it only on Orlando&#x27;s word. Each lead starts the next with `start-lead.sh`, so the line holds until someone changes it. `--status` shows it, and a Billing line it can&#x27;t read starts nothing.

- **plan** (or no line): his plan, with Remote Control, as everything above describes.
- **sdk**: his API key through the Agent SDK, which his plan&#x27;s promotional credit covers (Claude Code itself is not covered). The lead runs as `uv run &lt;skill&gt;/scripts/sdk-run.py` in the relay&#x27;s Rex tab, with the same first message, model, effort, `lead-settings.json` and permission mode auto, and with user, project and local settings loaded. Its cap is the Controls&#x27; `- Lead budget: **&lt;amount&gt;**` line (in US dollars, with a default when there is no line); a lead that spends it is stopped, and `sdk-run.py` adds an entry to the Log. Every run&#x27;s cost goes to `~/.claude/sdk-costs.log`. Claude Code reads the key from the Keychain itself, through an `apiKeyHelper` that `sdk-run.py` passes in the run&#x27;s settings, so the key is never in the environment of the commands, hooks and MCP servers the lead starts, and auto mode stays on (the `CLAUDE_CODE_SUBPROCESS_ENV_SCRUB` route forces permission mode to default, so don&#x27;t use it). There is no Remote Control: Orlando reads the lead&#x27;s pane in Rex, or the Log. Nothing typed into its pane reaches it, so &quot;Telling a live lead something&quot; and the memory fuse&#x27;s line in its pane don&#x27;t work: to stop one, set Relay to **off** (it stops at its next hand-off) or close its pane.
- **api**: refused, nothing starts. It ran interactive `claude` with an `apiKeyHelper`, which is billed as Claude Code, not covered by the credit: it once drained his paid balance until every call was refused.

The heartbeat&#x27;s relay watchdog checks the plan&#x27;s `/usage` before it starts a lead, so while the plan is at its limit it does not restart an sdk relay either: start that one by hand. It does restart an sdk lead stopped at its budget once no lead is alive, so each restart can spend another Lead budget (it starts a limited number a day).

## After a crash, a power cut or a usage limit

Look first: `--status`, and in a git repo `git log --oneline -5` and `git worktree list`. Then one of:

- **A usage limit, and the lead&#x27;s pane is still open.** A lead stopped by a limit in the middle of a turn stays alive and idle: `--status` shows it as running, &quot;idle for&quot; a long while, and `start-lead.sh` refuses every new start while it sits there. Remote Control sessions don&#x27;t resume by themselves after a limit. After the reset, either send it one line to carry on (see &quot;Telling a live lead something&quot;), or close its pane (`rex block close &lt;pane&gt;`) and start again as below.
- **Start again** with `start-lead.sh`, once no lead of this relay is alive. A fresh lead carries on the batch marked &quot;running&quot; from what is in the files.
- **Bring the stopped lead back,** when it stopped a short while ago with builders in the middle of their work. Resuming keeps the lead&#x27;s context and lets it continue each builder with its own, at the price of reading all of that context once more. Find the session&#x27;s id (the newest `.jsonl` in the project&#x27;s folder under `~/.claude/projects/`), then start it the way the script would, so it is named and guarded like any lead:

  ```
  rex run --focus=false --label &quot;&lt;Name&gt;: Batch N&quot; -c &lt;project folder&gt; -- ~/.local/bin/claude --remote-control &quot;&lt;Name&gt;: Batch N&quot; --permission-mode auto --settings &lt;skill&gt;/lead-settings.json --resume &lt;session id&gt; &quot;&lt;one plain line: what happened, what was lost, carry on&gt;&quot;
  ```

  An sdk lead can&#x27;t be resumed this way: start a fresh one.

  Tell it that everything running at the time is gone (its builders, its `caffeinate`, any checks in flight), and to continue each builder with SendMessage.

Either way, confirm with `rex capture` that the session came up and began work, and that it started its own `caffeinate`. `--status` says whether the memory fuse is watching: a lead brought back by hand has none until you start it, with `( &lt;skill&gt;/scripts/memory-fuse.sh watch &lt;/dev/null &gt;/dev/null 2&gt;&amp;1 &amp; )`. A session brought back by hand opens in a tab with no name: name it with `rex window rename &lt;window id&gt; &quot;&lt;Name&gt;: Batch N&quot;` (the id is in `rex window ls`), so Orlando can tell his tabs apart.

## What it needs

Rex (the sessions run in its panes), Remote Control (on the plan), `uv` (on sdk), permission mode &quot;auto&quot; (the lead isn&#x27;t asked before each step, so the project&#x27;s `CLAUDE.md` must say what is off limits), `caffeinate`, and `jq` (part of macOS), which names the relay&#x27;s tab and finds finished panes. It has been proven on one code project, Corillo. On other kinds of work, treat the first batch as a trial and read its Log entry before letting the rest run.
</code></pre>

## <code>builder&#45;brief&#46;md</code> {#builder-brief-md}

<a href="#builder-brief-md">Link to this file</a>

The brief every builder agent in a relay batch reads first, covering where to work, how to prove the work, and what to put in the final report.

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere"># Brief for every builder in a relay batch

You are one of up to three agents working on the same project at the same time. The lead (another Claude session) gave you one item and will bring your work together with the others&#x27;. Orlando, the owner, isn&#x27;t there to answer questions: make sound calls yourself and write down the ones he should see.

## Read first

The project&#x27;s `CLAUDE.md`, its `context.md` if it has one, &quot;How this project runs&quot; and your batch in `roadmap.md`, and whatever your prompt names. The project&#x27;s rules win over this brief. Read long reference files by section (find the part you need with grep on the headings), not whole.

## Where you work

- Only in the place your prompt names: your worktree and branch, or your own files. With a worktree, never the main checkout. Never another agent&#x27;s files, an installed or live copy of the project, or real data.
- Shell state doesn&#x27;t carry between commands: start each with `cd &lt;your place&gt; &amp;&amp;`, and use absolute paths with the file tools.
- Scratch files go where the project says, or under `~/ai/tmp/&lt;project&gt;/&lt;your name&gt;/`. Not in the project folder.
- Don&#x27;t edit `roadmap.md` or `context.md`: the lead writes those from your report.
- Never deliver, install, publish, push or merge. The lead does that once the whole batch passes.

## How to work

- **Build what the item says, no more.** No drive-by rewrites, renames or tidying. If you find a real problem on the way, fix it only if it is small and in your files. Otherwise report it.
- **Prove it.** For code: write the test that shows the thing missing or wrong, see it fail, build, see it pass. Orlando doesn&#x27;t read code, so tests are his review. For anything else: check the result against what the item promised and say how you checked (the source for each claim, the numbers worked again, the file opened and read).
- **Decisions.** The roadmap&#x27;s table decides, and a decision without an answer goes by its recommendation. For a small call nobody covered, choose what Orlando would most plausibly want, keep it small, and list it under &quot;Calls for Orlando&quot;. For a big one, don&#x27;t build it: report it.
- **Other agents are working on this computer right now.** While iterating, run only the checks you need. Before you finish, run the project&#x27;s full checks once. A check that measures time and fails under load: run it alone twice before deciding, and say so in your report rather than changing it. Never loosen or delete a check to get a pass. If a check is wrong, say why.
- **Never find a limit by hitting it.** A command whose memory grows with its input can ask for hundreds of GB within seconds. macOS then raises its out-of-memory alarm for the whole Mac and stops other programs, while most memory is still free. So when your item is about very large input, hostile input or load: measure at three or four small sizes (`/usr/bin/time -l &lt;command&gt;` prints the peak memory), work out the rest, and prove the limit with a test at a small size. Small means a peak under 200 MB. One size per command, smallest first, never a loop over sizes, and stop the series at the first size that passes 500 MB or is stopped. A command that ends with exit code 137 or &quot;Killed: 9&quot; was stopped for memory, by macOS or by the relay&#x27;s memory fuse (it stops any one process that passes a quarter of the Mac&#x27;s memory). Never run it again as it was: make it smaller, and say in your report what happened.
- **Never use `git stash`.** It is shared by every worktree of a repo, so one agent&#x27;s stash can land in another&#x27;s work. To set work aside, make a temporary commit on your branch.
- **Clean up after yourself.** Whatever you made that isn&#x27;t the deliverable (temporary folders, test data, builds, downloads, programs you started) is gone before you finish. Leftovers that grow with use are a leak.
- **Never take the computer&#x27;s focus.** Don&#x27;t bring any app to the front.
- **Never put Orlando&#x27;s name, email or file paths in a request to an outside service.**
- **Words people read** are plain and short, with no em dashes.
- **In git, commit as you go** on your branch, with short subject lines that say what the person now gets. Don&#x27;t merge, rebase or touch the main branch.

## Your report (your final message)

Under 400 words:

1. **Built:** each item, one line on what the person now gets, and the check that proves it.
2. **Not done:** what, and why.
3. **Calls for Orlando:** each call you made: what you chose, why, and what the other option was.
4. **Checks:** the result of your last full run, and anything that failed under load but passed alone.
5. **Left over:** anything the lead should pick up.

Say plainly what is verified and what isn&#x27;t. If the full checks don&#x27;t pass, say so and name what fails. Don&#x27;t present unfinished work as done.
</code></pre>

## <code>lead&#46;md</code> {#lead-md}

<a href="#lead-md">Link to this file</a>

The instructions a relay lead session follows to lead one batch of a roadmap, from reading the plan to handing off to the next session.

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere"># Leading one batch of a relay

You are the lead for exactly one batch of this project&#x27;s `roadmap.md`. A script started you in a fresh session. Orlando isn&#x27;t at the computer and may look in over Remote Control, so keep your messages short and lead with anything he needs to know.

Each batch gets a fresh lead so that no session&#x27;s context grows through the whole roadmap. That only works if everything the next lead needs is in the project&#x27;s files and in git, not in your head. Write things down as you go.

The project&#x27;s own rules win over this file: its `CLAUDE.md`, and the &quot;How this project runs&quot; section of `roadmap.md`.

`&lt;skill&gt;` below means the folder this file is in.

## Before anything else

1. **Keep the computer awake.** Start `caffeinate -ims -t &lt;seconds&gt;` in the background, sized to the batch (6 hours if you can&#x27;t tell), and note its process id. Start your own even if another is running: another session stops its own before your batch ends. The display may sleep: Remote Control doesn&#x27;t need it (Orlando&#x27;s call).
2. **Read** the project&#x27;s `CLAUDE.md`, its `context.md` if it has one, and `roadmap.md`: the Controls, &quot;How this project runs&quot;, the decisions, your batch, and the Log&#x27;s last entry. Don&#x27;t read long reference files whole (a spec, research notes): the builders read the parts they need.
3. **Find your batch:** the first one whose status is &quot;running&quot; or &quot;queued&quot;. One marked &quot;queued, low&quot; is skipped.
4. **Look at where things stand.** In a git repo: `git status`, `git log --oneline -15`, `git worktree list`, `git branch`, and `git status --short` in each worktree. Anywhere: which of the batch&#x27;s files already exist, and free disk space (`df -h`).
   - If the batch is &quot;queued&quot;, mark it `Status: **running** (&lt;today&#x27;s date&gt;, session &quot;&lt;your session&#x27;s name&gt;&quot;)` and commit that. The Status stays where it is, on the line right under the batch&#x27;s heading: the scripts read it nowhere else.
   - If it is &quot;running&quot;, an earlier session was stopped part way (a power cut, a usage limit). First count the pick-ups: if this pick-up finds nothing new since the one before (no commit, no changed file, no Log line), write `(pick-up 2)` in the parentheses after `**running**`, never inside the bold, and add one at each such pick-up in a row; a pick-up that finds progress clears the count. At the third in a row, set Relay to **off**, say why in the Log, commit, and stop cleanly as &quot;Keep the cost down&quot; says. Otherwise carry on from what is there: what is merged, the worktrees and their branches, uncommitted work, the Log. Don&#x27;t start the batch again. That session&#x27;s agents ended with it, so for each unfinished item brief a fresh builder to finish from what is there: tell it what is committed, what isn&#x27;t, and that any check that was running must be run again.

## Do the batch

1. **Builders.** One agent per numbered item, at most three at once, with the model and effort the Controls give (Agent tool). An item that is a few minutes&#x27; work you may do yourself. Each builder&#x27;s prompt:
   - read `&lt;skill&gt;/builder-brief.md` first, then the project&#x27;s rules;
   - its item, quoted from `roadmap.md`, and the notes it points to;
   - where it works, the files that are its own, and the files the other builders are in;
   - the decisions that apply (a decision Orlando hasn&#x27;t answered goes by its recommendation);
   - what the other builders&#x27; work will show where the two meet. Message a builder that is still running when another&#x27;s work lands and changes what it will see.

   Where they work depends on the project:
   - **Code in its own git repo:** each builder in its own worktree under `.claude/worktrees/&lt;name&gt;`, on its own branch (`relay/b&lt;batch&gt;-&lt;short-name&gt;`). Link in large untracked folders (such as `node_modules`) rather than installing them again.
   - **Anything else** (documents, research, a folder inside a larger repo): no worktrees. Each builder writes only its own files.

   Builders never deliver, never merge, and never edit `roadmap.md` or `context.md`. You do that from their reports.
2. **Bring it together.** With worktrees: merge each branch with `git merge --no-ff` when its builder reports its checks green. Settle small conflicts yourself (both sides added something: keep both) and send large ones back to the builder. Build straight after each merge: two branches can merge without conflict and still not work together. Then run the project&#x27;s full checks once, on the whole. Failures that no builder had alone are expected: find the cause, never loosen a check.
3. **Review.** One fresh reviewer, read-only, on everything the batch changed, briefed with `&lt;skill&gt;/reviewer-brief.md` plus what to review (the commit range, or the files) and where to write its findings: a scratch file outside the project, unless the project names a place. What matters from it goes in the Log, so the file needn&#x27;t be kept. Fresh eyes find real problems in work that passes its checks, so don&#x27;t skip this for a batch that built something. Give what is serious to one fix agent, then run the checks again. List the rest in the Log. If the reviewer couldn&#x27;t write its findings file, its last message carries them.
4. **Deliver.** When the checks pass on the final state, do the delivery step that &quot;How this project runs&quot; describes, if it says that step may be done without asking. If it doesn&#x27;t say, don&#x27;t deliver: write in the Log that the batch is ready and what delivering it would take.
5. **Write it down.**
   - In `roadmap.md`: the batch&#x27;s `Status: **done** (&lt;date&gt;; &lt;the result in a few words&gt;)`, and an entry in the Log: what was built, what was left and why, the calls you made for Orlando (what you chose, why, and the other option), what was delivered, and what he should try by hand.
   - `context.md`, if the project has one, brought up to date: the state, decisions, where things are. Keep it short.
   - Work you think should come next goes in the Log as a suggestion. Only Orlando adds batches to the queue. If he has told you to queue something, write it as a batch in the form the others have.
   - Commit, with only this project&#x27;s files. Never `git add -A` in a repo that holds other work.
6. **Clean up.** Remove the worktrees and merged branches. Leave no temporary folders, test data or builds, and no programs running: look for what your agents started. Check free disk space again.

## Decide without him

Orlando isn&#x27;t there, so nothing waits for him. A decision in the roadmap&#x27;s table with no answer goes by its recommendation. For a call nobody covered, choose what he would most plausibly want, keep it small and easy to undo, and put it in the Log. A call that is big or can&#x27;t be undone isn&#x27;t yours: leave that item, say why in the Log, and go on with the rest.

## Keep the cost down

It is Orlando&#x27;s usage. Use no more agents than the batch needs, no second full run of the checks where one will do, and short messages.

**Spend usage well** (Orlando):

- A test run that passes should cost a few lines: the runner&#x27;s quiet reporter (for vitest, `--reporter=dot`), or the full output sent to a file in the temp folder with only its last 40 lines printed. Read the file only when a run fails, and remove it afterwards.
- The agent that needs a full suite runs it and reports the counts and the names of what failed. A lead never pastes a suite&#x27;s output into its own conversation.
- Read the part of a file the work needs, not the whole of a long one twice.
- Unless a roadmap&#x27;s Agents line says otherwise: builders and fix agents on Opus 5.5 at effort high, a batch&#x27;s one reviewer at xhigh.

**On the API key (sdk).** When the Controls say `- Billing: **sdk**`, you run through the Agent SDK (`&lt;skill&gt;/scripts/sdk-run.py`) on Orlando&#x27;s API key, not his plan: his plan&#x27;s promotional credit covers it, and every token is money, so the rules above count double. Your agents run in your process and bill the key too, with nothing for you to set. You have a hard cap, the Controls&#x27; `- Lead budget` (in US dollars, with a default when there is no such line): when it is spent you are stopped mid-turn with no chance to write anything, and `sdk-run.py` adds a short entry to the Log saying so. So commit finished work as you go, and keep the Log&#x27;s account of where things stand current at each step, not only at the end. You have no Remote Control (it needs the plan), and nothing typed into your pane reaches you: Orlando reads your pane in Rex and the Log. Your cost is written to `~/.claude/sdk-costs.log` when you end; in your Log entry say that&#x27;s where it is. Never read, print or pass the key, and never run the Keychain command yourself: Claude Code reads it through the `apiKeyHelper` that `sdk-run.py` sets, and the commands you run don&#x27;t see it. The next lead keeps the Billing and Lead budget lines as they are: change them only on Orlando&#x27;s word.

**When an action is denied.** You run with permission questions turned into a &quot;no&quot; (the skill&#x27;s `lead-settings.json`), so nothing waits for Orlando. When an action the batch needs is denied, don&#x27;t look for a way round it: finish what can be finished, say in the Log what was denied and why it was needed, and stop cleanly if the batch can&#x27;t be done without it.

If you are about to run out of usage, or the batch is going badly: stop cleanly. Commit what is finished and passing, write in the Log exactly where things stand, leave the status &quot;running&quot;, stop your `caffeinate`, and run `&lt;skill&gt;/scripts/start-lead.sh &lt;project folder&gt; --close-me`. That starts nothing while your batch is marked running. It puts your pane away once you have been idle for 15 minutes, so no stopped session is left open in Rex. Then write your last message and end.

The next start carries on from there, and it may come without anyone asking: if the heartbeat&#x27;s watchdog watches this relay, it starts a fresh lead once no lead is alive and the usage allows. So if a fresh lead would only meet the same trouble, also set Relay to **off** in the Controls and say why in the Log.

## Keep the computer usable

It is Orlando&#x27;s own Mac. His apps and other relays share it. A process that grows without bound can raise macOS&#x27;s out-of-memory alarm for the whole Mac within seconds, while most memory is still free: a test of a very long answer once did that nine times.

- **The memory fuse** (`&lt;skill&gt;/scripts/memory-fuse.sh`) started with you. It stops any single process that you or your agents started once it passes a quarter of the Mac&#x27;s memory, and types one line into your pane when it does. When that line comes: tell the agent whose command it was, see that the command isn&#x27;t run again as it was, and say in the Log what was stopped and why it grew. `&lt;skill&gt;/scripts/memory-fuse.sh say` shows what it sees.
- **Exit code 137, or &quot;Killed: 9&quot;,** means a command was stopped for memory, by the fuse or by macOS. The same goes: never again as it was.
- **Limits are tested small.** An agent whose item is about very large input, hostile input or load must not find a limit by hitting it. The builder brief says how; point to that rule in its prompt.

## Never

- Take the computer&#x27;s focus: no app brought to the front, panes opened with `--focus=false`.
- Put Orlando&#x27;s name, email or file paths in a request to an outside service.
- Push, rewrite git history, or delete what this relay didn&#x27;t create.
- Stop another session&#x27;s `caffeinate` or programs: only your own, by process id.

## Hand off

Every lead ends the same way, whether or not another batch follows, so no finished session is left open in Rex for Orlando to close or to tell apart.

1. Read the Controls again: Orlando may have changed them while you worked. If the relay stops here (Relay is off, your batch is the one to stop after, or no other batch is queued), write in the Log why, and commit.
2. Stop your `caffeinate` (the next lead starts its own).
3. Run `&lt;skill&gt;/scripts/start-lead.sh &lt;project folder&gt;` once, in every case. It reads the Controls itself:
   - If another batch is to run, it starts the next lead in a pane beside yours, in this relay&#x27;s Rex tab, says that the session came up, and prints the command that reads its pane. Read that pane once to see that it has begun work.
   - If not, it starts nothing and says why.

   Either way it puts your session away: your pane closes by itself once you have been idle for 15 minutes.
4. Finish with a short message for Orlando: what he can do now that he couldn&#x27;t before, what he should try by hand, and what waits for his decision. Then end your turn and do nothing more.
</code></pre>

## <code>reviewer&#45;brief&#46;md</code> {#reviewer-brief-md}

<a href="#reviewer-brief-md">Link to this file</a>

The brief for the fresh reviewer who reads a finished relay batch, with the rules it works under, what to look for, and the form of its findings.

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere"># Brief for the reviewer of a relay batch

A few agents have just built one batch of work, each on its own item, and the lead has brought their work together. Every item came with its own checks and they pass. But each builder saw only its own part, the parts had never been together until now, and nobody has read the result with fresh eyes. Work that passes its checks still hides real problems. Finding them before the batch is delivered is your job.

Your prompt says what to review (a commit range, or a list of files) and where to write your findings. Read the project&#x27;s `CLAUDE.md` and `context.md` first, then the batch&#x27;s entry in `roadmap.md` with the notes it points to: they say what the person was promised. The project&#x27;s rules win over this brief.

Judge the batch twice: is the work right, and does the person get what the item promised?

## Rules

- **Read-only.** Don&#x27;t edit, create or delete anything in the project, and don&#x27;t check out other commits there. Use `git show` and `git diff` to see other versions. The one file you write is your findings.
- **Don&#x27;t run the project&#x27;s build, checks or app:** they belong to the lead right now. To test a suspicion you may run a small scratch script outside the project, and remove it when you finish. Keep it small in every way: never feed a program a huge input to see what happens.
- Never put Orlando&#x27;s name, email or file paths in a request to an outside service.

## How to review

Read what changed in full, with what is around it, not only the changed lines: most mistakes in a change are in what it didn&#x27;t look at. Then look for, in this order:

1. **Mistakes the batch introduced:** wrong results, lost or damaged content, an edge the work forgot (empty, very long, unusual characters, two things at once), a failure that now says nothing.
2. **Parts that don&#x27;t fit together.** Different agents&#x27; work, joined without having been designed together. Read it as one piece and say where the parts disagree, repeat each other or leave a gap.
3. **Leaks:** anything added that holds memory, files, disk space or a running program and never lets go, or grows without bound.
4. **Slowness:** anything added that runs often and grows with the size of the data.
5. **Safety,** where the work touches it: a way round a check, or something a hostile file or input could now do.
6. **Words people read** that this batch added: unclear, technical, inconsistent with the rest, or untrue.
7. **Checks that prove nothing:** a new test that would pass with the work removed, an old check loosened or skipped, a claim with no source.
8. **The record:** something written in the spec, the docs or the roadmap that the work doesn&#x27;t do.

For work that isn&#x27;t code (a document, research, an analysis), the same order holds: what is wrong, what doesn&#x27;t fit together, what is claimed without support, and what was promised and isn&#x27;t there.

Don&#x27;t report style, naming, or what you would have done differently.

## Output

Write your findings to the file your prompt names, most serious first:

```
### &lt;ID&gt;: &lt;short title&gt;
- Severity: P0 (loss of data, safety) | P1 (a problem people will hit) | P2 (an edge case, a leak, slowness, wrong words) | P3 (minor)
- Where: path:line (every place involved)
- What happens: the concrete failure, as steps or inputs and the wrong result
- Confidence: reproduced | read (certain from reading) | suspected (say what would confirm it)
- Fix: the direction you would take
- Check: what would catch it
```

Start the file with one sentence: would you let Orlando use this as it is, and if not, what must be fixed first. End it with &quot;Looked at and found sound&quot;: one line for each part you read closely and found right.

Be honest about confidence, and don&#x27;t pad: if the batch is sound, a short file is the right answer. Your final message to the lead, under 200 words: the count by severity, the most important findings as one line each with their ids, and anything the lead must know. If you couldn&#x27;t write the file, put the findings in that message instead.
</code></pre>

## <code>roadmap&#45;template&#46;md</code> {#roadmap-template-md}

<a href="#roadmap-template-md">Link to this file</a>

The blank form for a project's <code>roadmap&#46;md</code>, with its Controls, decisions table, batches, and Log, which the relay scripts read.

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere"># &lt;Project&gt; roadmap

&lt;Two or three sentences: where this plan came from, and the steer from Orlando it follows.&gt;

This file is also the queue. A fresh Claude session leads one batch, checks it, delivers it, marks it done here, then starts the next session (the `relay` skill).

## Controls (Orlando edits these, or tells the live session)

- Name: **&lt;Project&gt;** (sessions are named &quot;&lt;Project&gt;: Batch 1&quot;, and so on)
- Relay: **on**
- Stop after batch: **none** (write a batch number in the bold to pause after it, such as **2**; any other form starts nothing)
- Agents: &lt;model&gt; at &lt;effort&gt;, at most three builders and one reviewer per batch
- Lead: **opus high** (the lead&#x27;s model and effort, so its cost doesn&#x27;t move with the level saved elsewhere; **default** uses the saved ones)
- Billing: **plan** (who pays for the leads: **plan**, with Remote Control, or **sdk**, Orlando&#x27;s API key through the Agent SDK, without it)
- Lead budget: **&lt;amount&gt;** (US dollars, the most one lead may spend on **sdk**; the plan ignores it)

To stop the relay after the current batch, change **on** to **off** in the Relay line. Keep that line to those words alone: with anything else on it, nothing starts.

## How this project runs

- **Checks:** &lt;what must pass before a batch counts as done: a command such as `npm test`, or what a fresh reader must confirm&gt;
- **Deliver:** &lt;what happens when a batch passes, and whether it may be done without asking. &quot;Nothing&quot; if there is no such step.&gt;
- **Where builders work:** &lt;worktrees of this repo, or each in its own files; the scratch folder; anything to link in rather than install again&gt;
- **Never:** &lt;what no session may do here&gt;

## Decisions that are yours

Each has a recommendation. A batch that depends on one goes ahead with the recommendation unless you change the answer here.

| # | Decision | Recommendation | Your answer |
|---|---|---|---|
| D1 | **&lt;The decision, in a few words.&gt;** &lt;What it changes for you.&gt; | &lt;Yes, No, or the option&gt; | (recommended) |

## Batches

Sizes: S is one agent&#x27;s short piece of work, M two or three times that, L a batch of its own.

### Batch 1: &lt;what you get from it&gt;
Status: **queued**

1. **&lt;Item&gt;** (needs D1). &lt;What you see today, and what you will see.&gt; Size S. Owner of: &lt;the files or folders that are this item&#x27;s own&gt;.
2. **&lt;Item&gt;.** &lt;...&gt; Size M. Owner of: &lt;...&gt;.

Check before delivering: &lt;anything beyond the usual checks, or leave this line out&gt;

### Batch 2: &lt;what you get from it&gt;
Status: **queued**

1. **&lt;Item&gt;.** &lt;...&gt;

### Later
Status: **not queued**

- &lt;Work that is wanted but not yet agreed, or that needs Orlando: an account, a purchase, his judgment.&gt;

## Log

(Each session adds an entry when its batch is done: what was built, what was left and why, the calls made for Orlando, what was delivered, and what he should try by hand.)
</code></pre>

## Scripts

These scripts are one person's working code, shared from Orlando's setup with private details replaced by placeholders. They are not a supported tool: read each one before you run it, and expect to change it to fit your own setup.

### <code>lead&#45;settings&#46;json</code> {#lead-settings-json}

<a href="#lead-settings-json">Link to this file</a>

The settings every relay lead session starts with, which turn any permission question into a "no" so a lead never waits on an answer.

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere">&#123;
  &quot;permissions&quot;: &#123;
    &quot;denyInsteadOfPrompting&quot;: true
  &#125;
&#125;
</code></pre>

### <code>close&#45;pane&#45;later&#46;sh</code> {#close-pane-later-sh}

<a href="#close-pane-later-sh">Link to this file</a>

Closes a Rex pane once the session in it has been idle for a while, so finished lead sessions don't pile up. <code>start&#45;lead&#46;sh</code> starts it, and it can also be run by hand.

**Needs to run:** Rex, jq, and macOS.

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere">#!/bin/zsh -f
# Closes a Rex pane once the session in it has been idle for a while. This is
# how a finished lead session is put away (start-lead.sh starts it), so old
# sessions don&#x27;t pile up for Orlando to close by hand.
#
#   close-pane-later.sh &lt;block id&gt; [idle seconds, default 900]
#   close-pane-later.sh &lt;block id&gt; [idle seconds] --say   say what it would do now, and do nothing
#
# It runs on its own, in no pane. A timer in a tab of its own was one more tab
# to wonder about, and closing that tab stopped the clean-up.
#
# &quot;Idle&quot; is read from what Claude Code reports to Rex about the pane. A pane
# is never closed while its session is working, and the wait is counted from
# the end of its last turn, so a finished lead that Orlando asks something is
# not cut off while it answers. A pane that reports nothing is closed once the
# wait has passed. It gives up after six hours, and ends at once if the pane
# has gone.
zmodload zsh/datetime
trap &#x27;&#x27; HUP

say=0
args=()
for a in &quot;$@&quot;; do
  if [[ &quot;$a&quot; == --say ]]; then say=1; else args+=(&quot;$a&quot;); fi
done
block=&quot;$&#123;args[1]&#125;&quot;
wait=&quot;$&#123;args[2]:-900&#125;&quot;
[[ -n &quot;$block&quot; ]] || &#123; print -u2 &quot;Which pane? Give its block id.&quot;; exit 1 &#125;
[[ &quot;$wait&quot; == &lt;-&gt; ]] || &#123; print -u2 &quot;The wait is a number of seconds.&quot;; exit 1 &#125;

rex_bin=$(command -v rex) || rex_bin=&quot;$HOME/Library/Application Support/com.superlogical.rex/RexServer/Versions/Current/Rex Server.app/Contents/MacOS/rex&quot;
jq_bin=$(command -v jq) || jq_bin=&quot;&quot;

started=$EPOCHSECONDS
give_up=$(( started + 21600 ))
misses=0

while (( EPOCHSECONDS &lt; give_up )); do
  # What the pane reports. No answer means the pane has gone or Rex isn&#x27;t
  # running: look twice more, then there is nothing left to close.
  if ! st=$(&quot;$rex_bin&quot; block call -b &quot;$block&quot; program_status --autostart=false 2&gt;/dev/null); then
    (( misses += 1 ))
    if (( say || misses &gt;= 3 )); then
      (( say )) &amp;&amp; print &quot;The pane has gone, or Rex isn&#x27;t answering. Nothing to do.&quot;
      exit 0
    fi
    sleep 20
    continue
  fi
  misses=0

  count=0 working=0 last=&quot;&quot;
  if [[ -n &quot;$jq_bin&quot; ]]; then
    print -r -- &quot;$st&quot; | &quot;$jq_bin&quot; -r &#x27;[.records[]?] as $r | &quot;\($r | length) \([$r[] | select(.state == &quot;working&quot;)] | length) \([$r[] | .updated_at // empty] | max // &quot;&quot;)&quot;&#x27; 2&gt;/dev/null | read -r count working last
  fi

  now=$EPOCHSECONDS
  if [[ &quot;$count&quot; != &lt;-&gt; ]] || (( count == 0 )); then
    left=$(( started + wait - now ))
    why=&quot;the pane reports no status, so this is a plain timer&quot;
  elif (( working &gt; 0 )); then
    left=$wait
    why=&quot;its session is working&quot;
  else
    # The end of its last turn, in this Mac&#x27;s own time: &quot;2026-11-12T06:47:19...&quot;.
    idle=-1
    if [[ -n &quot;$last&quot; ]] &amp;&amp; strftime -r -s at &#x27;%Y-%m-%dT%H:%M:%S&#x27; &quot;$&#123;last[1,19]&#125;&quot; 2&gt;/dev/null; then
      idle=$(( now - at ))
    fi
    if (( idle &lt; 0 )); then
      left=$(( started + wait - now ))
      why=&quot;its last turn&#x27;s time can&#x27;t be read, so this is a plain timer&quot;
    else
      left=$(( wait - idle ))
      why=&quot;idle for $idle seconds&quot;
    fi
  fi

  if (( left &lt;= 0 )); then
    if (( say )); then print &quot;Would close it now ($why).&quot;; exit 0; fi
    &quot;$rex_bin&quot; block close &quot;$block&quot; --autostart=false &gt;/dev/null 2&gt;&amp;1
    exit 0
  fi
  if (( say )); then print &quot;Would wait $left more seconds ($why).&quot;; exit 0; fi
  (( left &gt; wait )) &amp;&amp; left=$wait
  (( left &lt; 5 )) &amp;&amp; left=5
  sleep $left
done
exit 0
</code></pre>

### <code>memory&#45;fuse&#46;sh</code> {#memory-fuse-sh}

<a href="#memory-fuse-sh">Link to this file</a>

Watches the Mac's biggest processes while a relay lead runs and stops any single process a lead started that grows past a quarter of the Mac's memory. <code>start&#45;lead&#46;sh</code> starts it, and it can be run with <code>say</code> to see what it would stop.

**Needs to run:** Rex, jq, and macOS.

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere">#!/bin/zsh -f
# The relay&#x27;s memory fuse.
#
# While a relay lead is running, it reads the biggest processes on this Mac
# every two seconds. A single process that a relay lead started and that has
# grown past the limit (a quarter of the Mac&#x27;s memory) is stopped at once,
# with everything it started. The lead is told in one line in its pane, and a
# note goes in ~/.claude/relay-fuse.log.
#
# Why it exists: a test fed a very long answer to a
# shell line whose memory grows with the square of its input. One zsh asked
# for over 200 GB within seconds. Nearly all of it was empty pages, so most of
# the Mac&#x27;s memory stayed free, but macOS counted them, declared itself out of
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
# For its tests: &quot;watch --trial&quot; runs beside the real fuse without counting as
# one, and RELAY_FUSE_MB (the limit, in MB), RELAY_FUSE_PATTERN (which sessions
# count as leads), RELAY_FUSE_LOG, RELAY_FUSE_REX, RELAY_FUSE_ROUND (readings
# in a round) and RELAY_FUSE_EVERY (seconds between readings) change what it
# does. A trial always sets its own pattern, so it can never touch a real
# lead&#x27;s work.
zmodload zsh/datetime
trap &#x27;&#x27; HUP

self=&quot;$&#123;0:A&#125;&quot;
mode=&quot;$&#123;1:-&#125;&quot;
pattern=&quot;$&#123;RELAY_FUSE_PATTERN:-^([^ ]*claude --(remote-control|name) |[^ ]*uv run [^ ]*sdk-run\.py .*--label ).*: Batch [0-9]+&#125;&quot;
log=&quot;$&#123;RELAY_FUSE_LOG:-$HOME/.claude/relay-fuse.log&#125;&quot;
limit=&quot;$&#123;RELAY_FUSE_MB:-$(( $(/usr/sbin/sysctl -n hw.memsize) / 1048576 / 4 ))&#125;&quot;
round=&quot;$&#123;RELAY_FUSE_ROUND:-150&#125;&quot;
every=&quot;$&#123;RELAY_FUSE_EVERY:-2&#125;&quot;
if [[ &quot;$limit&quot; != &lt;-&gt; || &quot;$round&quot; != &lt;-&gt; || &quot;$every&quot; != &lt;-&gt; ]]; then
  print -u2 &quot;The limit, the round and the wait are whole numbers.&quot;
  exit 2
fi
rex_bin=&quot;$&#123;RELAY_FUSE_REX:-$(command -v rex)&#125;&quot;
[[ -n &quot;$rex_bin&quot; ]] || rex_bin=&quot;$HOME/Library/Application Support/com.superlogical.rex/RexServer/Versions/Current/Rex Server.app/Contents/MacOS/rex&quot;
jq_bin=$(command -v jq) || jq_bin=&quot;&quot;

# gb &lt;MB&gt;: the same amount in GB, with one decimal.
gb() &#123; printf &#x27;%.1f&#x27; $(( $1 / 1024.0 )) &#125;

# note &lt;words&gt;: one dated line in the log, which keeps its last 200 lines.
note() &#123;
  print -r -- &quot;$(strftime &#x27;%Y-%m-%d %H:%M:%S&#x27; $EPOCHSECONDS) $1&quot; &gt;&gt; &quot;$log&quot;
  if (( $(/usr/bin/wc -l &lt; &quot;$log&quot;) &gt; 200 )); then
    /usr/bin/tail -n 200 &quot;$log&quot; &gt; &quot;$log.new&quot; &amp;&amp; /bin/mv &quot;$log.new&quot; &quot;$log&quot;
  fi
&#125;

# lead_of &lt;process id&gt;: the relay lead that process runs under. Sets REPLY.
lead_of() &#123;
  REPLY=&quot;&quot;
  local q=&quot;$1&quot; n=0
  while (( n &lt; 64 )); do
    q=$(/bin/ps -o ppid= -p &quot;$q&quot; 2&gt;/dev/null) || return 1
    q=&quot;$&#123;q// /&#125;&quot;
    [[ &quot;$q&quot; == &lt;-&gt; ]] || return 1
    (( q &gt; 1 )) || return 1
    if [[ &quot;$(/bin/ps -o args= -p &quot;$q&quot; 2&gt;/dev/null)&quot; =~ $pattern ]]; then
      REPLY=&quot;$q&quot;
      return 0
    fi
    (( n += 1 ))
  done
  return 1
&#125;

# name_of &lt;a lead&#x27;s process id&gt;: its session&#x27;s name, like &quot;Tasks: Batch 5&quot;.
name_of() &#123;
  /bin/ps -o args= -p &quot;$1&quot; 2&gt;/dev/null | /usr/bin/sed -n -E &#x27;s/.*--(remote-control|name|label) (.*: Batch [0-9]+).*/\2/p&#x27;
&#125;

# family &lt;process id&gt;: everything that process started, then the process.
family() &#123;
  local p
  for p in $(/usr/bin/pgrep -P &quot;$1&quot;); do family &quot;$p&quot;; done
  print -r -- &quot;$1&quot;
&#125;

# pane_of &lt;process id&gt;: the Rex pane that process runs in. Sets REPLY.
pane_of() &#123;
  REPLY=&quot;&quot;
  [[ -x &quot;$rex_bin&quot; &amp;&amp; -n &quot;$jq_bin&quot; ]] || return 1
  local s b p
  for s in $(&quot;$rex_bin&quot; ls --json --autostart=false 2&gt;/dev/null | &quot;$jq_bin&quot; -r &#x27;.sessions[]?.session_id&#x27; 2&gt;/dev/null); do
    for b in $(&quot;$rex_bin&quot; block ls -s &quot;$s&quot; --json --autostart=false 2&gt;/dev/null | &quot;$jq_bin&quot; -r &#x27;.blocks[]?.block_id&#x27; 2&gt;/dev/null); do
      p=$(&quot;$rex_bin&quot; block call -b &quot;$b&quot; process --autostart=false 2&gt;/dev/null | &quot;$jq_bin&quot; -r &#x27;.child.pid // empty&#x27; 2&gt;/dev/null) || p=&quot;&quot;
      if [[ &quot;$p&quot; == &quot;$1&quot; ]]; then REPLY=&quot;$b&quot;; return 0; fi
    done
  done
  return 1
&#125;

# tell_lead &lt;a lead&#x27;s process id&gt; &lt;one line&gt;: types the line into its pane.
tell_lead() &#123;
  pane_of &quot;$1&quot; || return 1
  &quot;$rex_bin&quot; send -b &quot;$REPLY&quot; &quot;$2&quot; &gt;/dev/null 2&gt;&amp;1 || return 1
  &quot;$rex_bin&quot; send-key -b &quot;$REPLY&quot; enter &gt;/dev/null 2&gt;&amp;1
&#125;

# check &lt;process id&gt; &lt;its memory in MB&gt;: stops it if it is over the limit and
# a relay lead started it. Ends with 3 when the process is not this fuse&#x27;s to
# touch: no relay lead started it, or it is a lead itself.
check() &#123;
  local pid=&quot;$1&quot; mb=&quot;$2&quot; args lead lead_name short victims line
  [[ &quot;$pid&quot; == &lt;-&gt; &amp;&amp; &quot;$mb&quot; == &lt;-&gt; ]] || return 2
  (( mb &gt; limit )) || return 0
  args=$(/bin/ps -o args= -p &quot;$pid&quot; 2&gt;/dev/null) || return 0
  [[ -n &quot;$args&quot; ]] || return 0
  if [[ &quot;$args&quot; =~ $pattern ]]; then
    if [[ &quot;$mode&quot; == say ]]; then
      print &quot;A lead itself is over the limit, and is left alone: process $pid at $(gb $mb) GB.&quot;
    else
      note &quot;left alone: a lead itself, process $pid ($(name_of &quot;$pid&quot;)), is at $(gb $mb) GB&quot;
    fi
    return 3
  fi
  lead_of &quot;$pid&quot; || return 3
  lead=&quot;$REPLY&quot;
  lead_name=$(name_of &quot;$lead&quot;)
  short=&quot;$&#123;$&#123;args%% *&#125;:t&#125;&quot;
  victims=($(family &quot;$pid&quot;))
  if [[ &quot;$mode&quot; == say ]]; then
    print &quot;Would stop: process $pid ($short) at $(gb $mb) GB, and $(( $&#123;#victims&#125; - 1 )) it started, under $&#123;lead_name:-lead $lead&#125;.&quot;
    return 0
  fi
  kill -KILL &quot;$&#123;victims[@]&#125;&quot; 2&gt;/dev/null
  note &quot;stopped process $pid ($short) at $(gb $mb) GB, limit $(gb $limit) GB, under $&#123;lead_name:-lead $lead&#125;, with $(( $&#123;#victims&#125; - 1 )) it had started: $&#123;$&#123;args[1,160]&#125;//$&#x27;\n&#x27;/ &#125;&quot;
  line=&quot;Relay fuse: stopped process $pid ($short) at $(gb $mb) GB. One process may use $(gb $limit) GB at most. Don&#x27;t let it be run again as it was: tell the agent that started it, and put it in the Log.&quot;
  tell_lead &quot;$lead&quot; &quot;$line&quot; || note &quot;could not type that into the lead&#x27;s pane (process $lead)&quot;
  return 0
&#125;

# What is over the limit in what top prints: &quot;&lt;process id&gt; &lt;MB&gt;&quot; a line.
over_limit=&#x27;
  function mb(s,    u, n) &#123;
    sub(/[+-]$/, &quot;&quot;, s)
    u = substr(s, length(s)); n = substr(s, 1, length(s) - 1) + 0
    if (u == &quot;T&quot;) return n * 1048576
    if (u == &quot;G&quot;) return n * 1024
    if (u == &quot;M&quot;) return n
    return 0
  &#125;&#x27;

say() &#123;
  local leads p name pid mem any=0
  print &quot;One process under a relay lead may use $(gb $limit) GB at most.&quot;
  # -a: macOS&#x27;s pgrep leaves out its own ancestors otherwise, and this may be
  # run by something a lead started.
  leads=($(/usr/bin/pgrep -a -f &quot;$pattern&quot;))
  if (( $&#123;#leads&#125; )); then
    for p in $leads; do
      name=$(name_of &quot;$p&quot;)
      print &quot;Relay lead running: $&#123;name:-process $p&#125; (process $p).&quot;
    done
  else
    print &quot;No relay lead is running.&quot;
  fi
  if p=$(/usr/bin/pgrep -f &#x27;memory-fuse\.sh watch$&#x27;); then
    print &quot;The fuse is watching (process $&#123;p//$&#x27;\n&#x27;/, &#125;).&quot;
  else
    print &quot;The fuse is not watching. It starts with the next lead.&quot;
  fi
  /usr/bin/top -l 1 -o mem -n 25 -stats pid,mem,command | /usr/bin/awk -v limit=&quot;$limit&quot; &quot;$over_limit&quot;&#x27;
    $1 ~ /^[0-9]+$/ &amp;&amp; NF &gt;= 3 &#123; m = int(mb($2)); if (m &gt; limit) print $1, m &#125;&#x27; | while read -r pid mem; do
    check &quot;$pid&quot; &quot;$mem&quot; &amp;&amp; any=1
  done
  (( any )) || print &quot;Nothing a relay lead started is over the limit right now.&quot;
  return 0
&#125;

watch() &#123;
  local other quiet=0 began
  # One fuse for the whole Mac. A trial&#x27;s fuse neither counts nor checks.
  if [[ &quot;$&#123;1:-&#125;&quot; != --trial ]]; then
    sleep $(( ($$ % 10) / 10.0 ))
    for other in $(/usr/bin/pgrep -f &#x27;memory-fuse\.sh watch$&#x27;); do
      [[ &quot;$other&quot; == &quot;$$&quot; ]] || exit 0
    done
  fi
  note &quot;watching: one process under a relay lead may use $(gb $limit) GB at most&quot;
  while true; do
    # -a: a lead that started this fuse (by starting the next lead) still counts.
    if [[ -n &quot;$(/usr/bin/pgrep -a -f &quot;$pattern&quot;)&quot; ]]; then quiet=0; else (( quiet += 1 )); fi
    if (( quiet &gt;= 3 )); then
      note &quot;ended: no relay lead is running&quot;
      exit 0
    fi
    began=$EPOCHSECONDS
    # A fresh top each round, which ends by itself after its readings. It hands
    # over every reading as it is taken, and each process over the limit is
    # looked at once a round.
    /usr/bin/top -l &quot;$round&quot; -s &quot;$every&quot; -o mem -n 25 -stats pid,mem,command 2&gt;/dev/null | /usr/bin/awk -v limit=&quot;$limit&quot; -v self=&quot;$self&quot; &quot;$over_limit&quot;&#x27;
      $1 ~ /^[0-9]+$/ &amp;&amp; NF &gt;= 3 &#123;
        m = int(mb($2))
        if (m &gt; limit &amp;&amp; !($1 in seen)) &#123; seen[$1] = 1; system(&quot;\&quot;&quot; self &quot;\&quot; check &quot; $1 &quot; &quot; m) &#125;
      &#125;&#x27;
    # A round that ended early means top could not run: don&#x27;t spin.
    (( EPOCHSECONDS - began &lt; round * every / 2 )) &amp;&amp; sleep 5
  done
&#125;

case &quot;$mode&quot; in
  watch) watch &quot;$&#123;2:-&#125;&quot; ;;
  say)   say ;;
  check) check &quot;$&#123;2:-&#125;&quot; &quot;$&#123;3:-&#125;&quot; ;;
  *)     print -u2 &quot;Usage: memory-fuse.sh watch | say&quot;; exit 2 ;;
esac
</code></pre>

### <code>sdk&#45;run&#46;py</code> {#sdk-run-py}

<a href="#sdk-run-py">Link to this file</a>

Runs one Claude Code session through the Agent SDK on an API key, with a hard spending cap, and logs each run's cost. <code>start&#45;lead&#46;sh</code> uses it for relays set to <code>sdk</code> billing, and it can be run by hand.

**Needs to run:** uv, macOS, and the Keychain (through the <code>security</code> command).

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere"># /// script
# requires-python = &quot;&gt;=3.12&quot;
# dependencies = [&quot;claude-agent-sdk&quot;]
# ///
&quot;&quot;&quot;Run one Claude Code session through the Agent SDK, billed to the API key.

Orlando&#x27;s plan&#x27;s promotional API credit covers the Agent SDK but not Claude Code
itself (tested: a run like this one came out of the promo credit,
while interactive claude with an apiKeyHelper was billed as Claude Code; an
SDK run with the same apiKeyHelper came out of the promo credit too).

Usage:
  uv run sdk-run.py --cwd &lt;folder&gt; --budget &lt;usd&gt; [--model M] [--effort E]
      [--label TEXT] [--settings FILE] [--read-dir DIR ...] [--roadmap FILE]
      (--prompt TEXT | --prompt-file PATH)

- Claude Code reads the key from the login Keychain itself (an apiKeyHelper
  setting), so it is never in the environment of the commands, hooks and MCP
  servers the session starts, and auto mode stays on (tested; the
  CLAUDE_CODE_SUBPROCESS_ENV_SCRUB route switches auto mode off). It is never
  printed or written.
- Permission mode is &quot;auto&quot;. User, project and local settings load, so deny
  rules and hooks hold; --settings adds one more file (the relay&#x27;s
  lead-settings.json, which turns a permission question into a &quot;no&quot;).
  --read-dir lets the session read one more folder without asking (a Read
  allow rule, nothing more): a relay lead reads its skill&#x27;s lead.md and
  briefs. Nobody can answer a permission question in an SDK run, so without
  it reading outside --cwd is refused.
- Model and effort: the saved ones unless given.
- --budget is a hard cap: the run stops when it is spent.
- Progress prints to the terminal. One line per run goes to the cost log,
  ~/.claude/sdk-costs.log (SDK_COSTS_LOG overrides it).
- With --roadmap, a run stopped by its budget adds an entry to that
  roadmap&#x27;s Log (the relay&#x27;s leads).
&quot;&quot;&quot;
import argparse, asyncio, datetime, json, os, subprocess, sys
from claude_agent_sdk import query, ClaudeAgentOptions

COSTS_LOG = os.environ.get(&quot;SDK_COSTS_LOG&quot;) or os.path.expanduser(&quot;~/.claude/sdk-costs.log&quot;)


HEADLESS_NOTE = (
    &quot;This session runs headless through the Agent SDK. Nobody can answer you, and the run &quot;
    &quot;ends the moment you end your turn: anything still running in the background then &quot;
    &quot;(subagents, background shell commands) is stopped and its result lost, and no &quot;
    &quot;notification will ever wake you. So start every agent in the foreground &quot;
    &quot;(run_in_background false), several at once in one message when they are independent, &quot;
    &quot;and never end your turn to wait for something. End your turn only when your work &quot;
    &quot;is finished or you have written down where it stopped.&quot;
)


def main() -&gt; None:
    p = argparse.ArgumentParser()
    p.add_argument(&quot;--cwd&quot;, required=True)
    p.add_argument(&quot;--budget&quot;, type=float, required=True, help=&quot;hard cap in USD for this run&quot;)
    p.add_argument(&quot;--model&quot;, default=None)
    p.add_argument(&quot;--effort&quot;, default=None)
    p.add_argument(&quot;--label&quot;, default=&quot;&quot;)
    p.add_argument(&quot;--settings&quot;, default=None, help=&quot;one more settings file to load&quot;)
    p.add_argument(&quot;--read-dir&quot;, action=&quot;append&quot;, default=[], help=&quot;a folder the session may read without asking&quot;)
    p.add_argument(&quot;--roadmap&quot;, default=None, help=&quot;a relay&#x27;s roadmap.md: a budget stop is noted in its Log&quot;)
    g = p.add_mutually_exclusive_group(required=True)
    g.add_argument(&quot;--prompt&quot;)
    g.add_argument(&quot;--prompt-file&quot;)
    a = p.parse_args()
    if a.budget &lt;= 0:
        sys.exit(&quot;The budget must be more than 0.&quot;)
    prompt = a.prompt if a.prompt is not None else open(a.prompt_file).read()

    os.environ.pop(&quot;ANTHROPIC_API_KEY&quot;, None)

    kw = &#123;&#125;
    if a.model:
        kw[&quot;model&quot;] = a.model
    if a.effort:
        kw[&quot;effort&quot;] = a.effort
    settings = json.load(open(a.settings)) if a.settings else &#123;&#125;
    settings[&quot;apiKeyHelper&quot;] = &quot;/usr/bin/security find-generic-password -s example-keychain-service -w&quot;
    if a.read_dir:
        allow = settings.setdefault(&quot;permissions&quot;, &#123;&#125;).setdefault(&quot;allow&quot;, [])
        # &quot;//&quot; starts an absolute path in a permission rule.
        allow += [f&quot;Read(/&#123;os.path.abspath(d)&#125;/**)&quot; for d in a.read_dir]
    kw[&quot;settings&quot;] = json.dumps(settings)
    opts = ClaudeAgentOptions(
        cwd=os.path.abspath(a.cwd),
        permission_mode=&quot;auto&quot;, setting_sources=[&quot;user&quot;, &quot;project&quot;, &quot;local&quot;],
        max_budget_usd=a.budget,
        # One message can be big: a session reading a page picture gets it back
        # as one line. The default 1 MB cap once ended a run
        # mid-way (&quot;JSON message exceeded maximum buffer size&quot;).
        max_buffer_size=64 * 1024 * 1024,
        # Claude Code&#x27;s own system prompt (the SDK&#x27;s default is a bare one), plus
        # the one rule a run like this needs: it ends when the session ends its
        # turn, so background agents still working then are lost (a reviewer
        # was once cut off mid-read).
        system_prompt=&#123;&quot;type&quot;: &quot;preset&quot;, &quot;preset&quot;: &quot;claude_code&quot;, &quot;append&quot;: HEADLESS_NOTE&#125;,
        **kw,
    )
    print(f&quot;&#123;a.label or os.path.basename(a.cwd)&#125;: Agent SDK run on the API key, budget $&#123;a.budget:g&#125;.&quot;, flush=True)
    asyncio.run(run(prompt, opts, a))


async def run(prompt: str, opts: ClaudeAgentOptions, a) -&gt; None:
    start = datetime.datetime.now()
    cost, outcome = None, &quot;ended without a result&quot;
    try:
        async for m in query(prompt=prompt, options=opts):
            kind = type(m).__name__
            if kind == &quot;AssistantMessage&quot;:
                for b in m.content:
                    bk = type(b).__name__
                    if bk == &quot;TextBlock&quot; and b.text.strip():
                        print(f&quot;\n&#123;b.text.strip()&#125;\n&quot;, flush=True)
                    elif bk == &quot;ToolUseBlock&quot;:
                        arg = b.input.get(&quot;description&quot;) or b.input.get(&quot;command&quot;) or b.input.get(&quot;file_path&quot;) or b.input.get(&quot;prompt&quot;, &quot;&quot;)
                        print(f&quot;  &gt; &#123;b.name&#125;: &#123;str(arg)[:140]&#125;&quot;, flush=True)
            elif kind == &quot;ResultMessage&quot;:
                cost = getattr(m, &quot;total_cost_usd&quot;, None)
                outcome = getattr(m, &quot;subtype&quot;, &quot;result&quot;)
    except Exception as e:  # report, don&#x27;t hide
        outcome = f&quot;error: &#123;e&#125;&quot;
        print(outcome, file=sys.stderr, flush=True)
    mins = (datetime.datetime.now() - start).total_seconds() / 60
    label = a.label or os.path.basename(a.cwd)
    line = f&quot;&#123;start:%Y-%m-%d %H:%M&#125; &#123;label&#125; cost_usd=&#123;cost&#125; minutes=&#123;mins:.0f&#125; outcome=&#123;outcome&#125;&quot;
    print(&quot;\n&quot; + line, flush=True)
    with open(COSTS_LOG, &quot;a&quot;) as f:
        f.write(line + &quot;\n&quot;)
    if outcome == &quot;error_max_budget_usd&quot;:
        print(f&quot;Stopped: the budget of $&#123;a.budget:g&#125; is spent.&quot;, flush=True)
        if a.roadmap:
            note_budget_stop(a, start, cost)


def note_budget_stop(a, start, cost) -&gt; None:
    &quot;&quot;&quot;Adds an entry at the end of the roadmap (its Log is the last section).&quot;&quot;&quot;
    now = datetime.datetime.now()
    entry = (
        f&quot;\n### &#123;a.label or &#x27;A lead&#x27;&#125;: stopped at its budget (&#123;now:%Y-%m-%d %H:%M&#125;)\n\n&quot;
        f&quot;Written by sdk-run.py, not by the lead: the lead&#x27;s Agent SDK budget of $&#123;a.budget:g&#125; &quot;
        f&quot;(the Controls&#x27; Lead budget) ran out and the run stopped mid-work, started &#123;start:%H:%M&#125;, &quot;
        f&quot;cost $&#123;cost if cost is not None else &#x27;?&#x27;&#125;. Its batch is still marked running; what it &quot;
        f&quot;finished is in the files and git. The next start carries on from there.\n&quot;
    )
    try:
        with open(a.roadmap, &quot;a&quot;) as f:
            f.write(entry)
    except OSError as e:
        print(f&quot;Couldn&#x27;t write the budget stop into &#123;a.roadmap&#125;: &#123;e&#125;&quot;, file=sys.stderr, flush=True)


if __name__ == &quot;__main__&quot;:
    main()
</code></pre>

### <code>start&#45;lead&#46;sh</code> {#start-lead-sh}

<a href="#start-lead-sh">Link to this file</a>

Starts a fresh Claude Code session to lead the next batch of a project's roadmap, puts finished lead panes away, and with options says what it would start or where the relay stands.

**Needs to run:** Rex, uv, jq, git, and macOS.

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere">#!/bin/zsh
# Starts a fresh Claude Code session to lead the next batch of a project&#x27;s
# roadmap.md: the first batch marked running (left part way by a session that
# was stopped) or queued. A batch marked &quot;queued, low&quot; is neither.
#
# Each batch gets a session of its own, so no session&#x27;s context grows through
# the whole roadmap (a long-lived lead costs far more each time it wakes).
# The session runs with Remote Control on, named &quot;&lt;Name&gt;: Batch N&quot;. It reads
# lead.md in this skill&#x27;s folder, leads one batch, and runs this script again
# when it ends. With &quot;- Billing: **sdk**&quot; in the Controls it runs through the
# Agent SDK (sdk-run.py) on Orlando&#x27;s API key instead of his plan, without
# Remote Control, under a budget cap (see &quot;Who pays&quot; below).
#
# In Rex a relay keeps to one tab, named after the session leading now. The
# next lead opens as a pane beside the last one, without taking focus, and
# every finished lead&#x27;s pane closes by itself once it has been idle for
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
here=&quot;$&#123;0:A:h&#125;&quot;
skill=&quot;$&#123;here:h&#125;&quot;
project=&quot;&quot;
mode=&quot;start&quot;
close_me=0
for arg in &quot;$@&quot;; do
  case &quot;$arg&quot; in
    --dry-run)  mode=&quot;dry-run&quot; ;;
    --status)   mode=&quot;status&quot; ;;
    --close-me) close_me=1 ;;
    -*)         echo &quot;Unknown option: $arg&quot; &gt;&amp;2; exit 2 ;;
    *)          project=&quot;$arg&quot; ;;
  esac
done
project=&quot;$&#123;$&#123;project:-$PWD&#125;:A&#125;&quot;
roadmap=&quot;$project/roadmap.md&quot;
[[ -f &quot;$roadmap&quot; ]] || &#123; echo &quot;There is no roadmap.md in $project.&quot; &gt;&amp;2; exit 1; &#125;
[[ -f &quot;$skill/lead.md&quot; ]] || &#123; echo &quot;Can&#x27;t find lead.md in $skill.&quot; &gt;&amp;2; exit 1; &#125;

# How a roadmap is read. Only its own lines count, in the template&#x27;s form:
# the Controls from the first &quot;## Controls&quot; section, and batches from the
# first &quot;## Batches&quot; section, each with its Status on the line right under its
# heading. Text in a code block or a quote is skipped. So a Log entry, a quoted
# line or a pasted example can never switch the relay on, rename it, or queue
# a batch (a security review of the heartbeat found this). Anything
# written another way reads as off or not there. A batch Status, a Stop after
# batch line or a Lead line that can&#x27;t be read starts nothing and says so,
# rather than being passed over (a review of the setup).

# Shared by every reader below. Windows line endings are dropped. A code block
# opens on a line of three or more backticks or tildes and closes only on a
# line of the same mark, at least as long, with nothing after it, so a block
# shown inside another block can&#x27;t end it early.
awk_lib=&#x27;
  &#123; sub(/\r$/, &quot;&quot;) &#125;
  function fenceline(line,   m, c, k) &#123;
    if (!match(line, /^ ? ? ?(```+|~~~+)/)) return 0
    m = substr(line, RSTART, RLENGTH); sub(/^ +/, &quot;&quot;, m); c = substr(m, 1, 1); k = length(m)
    if (!fence) &#123; fence = 1; fchar = c; flen = k; return 1 &#125;
    if (c == fchar &amp;&amp; k &gt;= flen &amp;&amp; line ~ /^ ? ? ?(`+|~+)[ \t]*$/) fence = 0
    return 1
  &#125;
&#x27;

# The bold value of a Controls line: &quot;- Relay: **on**&quot; gives &quot;on&quot;.
control() &#123;
  awk -v key=&quot;$1&quot; &quot;$awk_lib&quot;&#x27;
    fenceline($0) &#123; next &#125;
    fence &#123; next &#125;
    /^## / &#123; sec = (!seen &amp;&amp; $0 ~ /^## Controls([ \t]|$)/); if (sec) seen = 1; next &#125;
    sec &amp;&amp; index($0, &quot;- &quot; key &quot;: **&quot;) == 1 &#123;
      v = substr($0, length(&quot;- &quot; key &quot;: **&quot;) + 1)
      if (match(v, /\*\*/)) &#123; print substr(v, 1, RSTART - 1); exit &#125; &#125;
  &#x27; &quot;$roadmap&quot;
&#125;

# Is the relay on? Only when the Controls hold one Relay line, and it is
# exactly &quot;- Relay: **on**&quot; with nothing after it. The heartbeat&#x27;s watchdog
# reads it the same way, and the two must agree, or a relay that stops is
# never started again. Prints &quot;on&quot;, &quot;off&quot;, or &quot;unclear&quot; for a Relay line that
# says on in other words, or with more on the line.
relay_state() &#123;
  awk &quot;$awk_lib&quot;&#x27;
    fenceline($0) &#123; next &#125;
    fence &#123; next &#125;
    /^## / &#123; sec = (!seen &amp;&amp; $0 ~ /^## Controls([ \t]|$)/); if (sec) seen = 1; next &#125;
    sec &amp;&amp; tolower($0) ~ /^[ \t]*([-*+][ \t]*)?(\*\*)?[ \t]*relay[ \t]*(\*\*)?[ \t]*:/ &#123;
      lines++
      if ($0 ~ /^- Relay: \*\*on\*\*[ \t]*$/) exact++
      if ($0 ~ /^- Relay: \*\*on\*\*/) says_on++
    &#125;
    END &#123; if (lines == 1 &amp;&amp; exact == 1) print &quot;on&quot;; else if (says_on) print &quot;unclear&quot;; else print &quot;off&quot; &#125;
  &#x27; &quot;$roadmap&quot;
&#125;
unclear_relay=&#x27;roadmap.md&#x27;&quot;&#x27;&quot;&#x27;s Relay line is not exactly &quot;- Relay: **on**&quot;: nothing starts. Keep that line to those words alone, once, in the Controls. The heartbeat&#x27;&quot;&#x27;&quot;&#x27;s watchdog reads it the same way.&#x27;

# The name sessions carry: the Controls&#x27; Name, or the folder&#x27;s name without
# its date. Kept to plain characters, since it is also searched for by name.
name=&quot;$(control Name)&quot;
[[ -n &quot;$name&quot; ]] || name=&quot;$&#123;$&#123;project:t&#125;##[0-9][0-9][0-9][0-9][0-9][0-9][0-9][0-9]-&#125;&quot;
name=&quot;$&#123;name//[^A-Za-z0-9 _-]/&#125;&quot;
[[ -n &quot;$name&quot; ]] || name=&quot;Relay&quot;

# The first batch that is running or queued, in the order of the file. Only
# headings under &quot;## Batches&quot; are batches: a Log entry headed &quot;### Batch 1,
# done&quot; is not one. A running batch was left part way by a session that was
# stopped (a power cut, a usage limit): the new session carries on from what
# is there.
#
# Every numbered batch, one per line: its status word, a tab, its heading. The
# known words are queued, running, done, &quot;queued, low&quot; and &quot;not queued&quot;; any
# other (or no Status line at all) prints &quot;?&quot;. So does &quot;**queued** (low)&quot;, a
# batch meant to wait whose note is outside the bold. So does a heading whose
# next line opens a code block, is a quote or a &quot;## &quot; heading, or that ends
# the file: the heartbeat&#x27;s watchdog refuses those too, and the two must agree.
all_batches=$(awk &quot;$awk_lib&quot;&#x27;
  name != &quot;&quot; &#123;
    w = &quot;?&quot;
    if ($0 ~ /^Status: \*\*[^*]+\*\*/) &#123;
      w = $0; sub(/^Status: \*\*/, &quot;&quot;, w); sub(/\*\*.*/, &quot;&quot;, w)
      rest = $0; sub(/^Status: \*\*[^*]+\*\*/, &quot;&quot;, rest)
      if (w == &quot;queued&quot; &amp;&amp; tolower(rest) ~ /(^|[^a-z])low([^a-z]|$)/) w = &quot;?&quot;
    &#125;
    if (w != &quot;queued&quot; &amp;&amp; w != &quot;running&quot; &amp;&amp; w != &quot;done&quot; &amp;&amp; w != &quot;queued, low&quot; &amp;&amp; w != &quot;not queued&quot;) w = &quot;?&quot;
    print w &quot;\t&quot; name
    name = &quot;&quot;
  &#125;
  fenceline($0) &#123; next &#125;
  fence &#123; next &#125;
  /^[ \t]*&gt;/ &#123; next &#125;
  /^## / &#123; batches = (!seen &amp;&amp; $0 ~ /^## Batches([ \t]|$)/); if (batches) seen = 1; next &#125;
  &#123; name = (batches &amp;&amp; $0 ~ /^### Batch [0-9]+/) ? $0 : &quot;&quot; &#125;
  END &#123; if (name != &quot;&quot;) print &quot;?\t&quot; name &#125;
&#x27; &quot;$roadmap&quot;)
found=$(print -r -- &quot;$all_batches&quot; | awk -F&#x27;\t&#x27; &#x27;$1 == &quot;running&quot; || $1 == &quot;queued&quot; &#123; print $1 &quot; &quot; $2; exit &#125;&#x27;)
unreadable=$(print -r -- &quot;$all_batches&quot; | awk -F&#x27;\t&#x27; &#x27;$1 == &quot;?&quot; &amp;&amp; match($2, /Batch [0-9]+/) &#123; print substr($2, RSTART, RLENGTH) &#125;&#x27; | paste -sd, - | sed &#x27;s/,/, /g&#x27;)
unreadable_batches=&quot;$&#123;unreadable:+roadmap.md: the Status line of $unreadable can&#x27;t be read: nothing starts. Right under the batch&#x27;s heading it must say Status: **queued**, **running**, **done**, **queued, low** or **not queued**, with any note after the bold.&#125;&quot;

# Stop after batch: &quot;none&quot;, a batch number, &quot;absent&quot; when there is no such
# line, or &quot;?&quot; for a line that is there but not in the template&#x27;s form
# (&quot;- Stop after batch: **2**&quot;, a note after the bold is fine).
stop_after=$(awk &quot;$awk_lib&quot;&#x27;
  fenceline($0) &#123; next &#125;
  fence &#123; next &#125;
  /^## / &#123; sec = (!seen &amp;&amp; $0 ~ /^## Controls([ \t]|$)/); if (sec) seen = 1; next &#125;
  sec &amp;&amp; tolower($0) ~ /^[ \t]*([-*+][ \t]*)?(\*\*)?[ \t]*stop[ \t]+after[ \t]+batch/ &#123; n++; line = $0 &#125;
  END &#123;
    if (!n) &#123; print &quot;absent&quot;; exit &#125;
    if (n &gt; 1) &#123; print &quot;?&quot;; exit &#125;
    if (line ~ /^- Stop after batch: \*\*none\*\*/) &#123; print &quot;none&quot;; exit &#125;
    if (line ~ /^- Stop after batch: \*\*[0-9]+\*\*/) &#123; v = line; sub(/^- Stop after batch: \*\*/, &quot;&quot;, v); sub(/\*\*.*/, &quot;&quot;, v); print v; exit &#125;
    print &quot;?&quot;
  &#125;
&#x27; &quot;$roadmap&quot;)
unclear_stop=&#x27;roadmap.md&#x27;&quot;&#x27;&quot;&#x27;s Stop after batch line can&#x27;&quot;&#x27;&quot;&#x27;t be read: nothing starts. Write it as &quot;- Stop after batch: **none**&quot; or with a batch number in the bold, such as &quot;- Stop after batch: **2**&quot;.&#x27;

# The lead&#x27;s model and effort, from the Controls&#x27; Lead line: default (or no
# line), &lt;model&gt;, &lt;model&gt; &lt;effort&gt;, or &lt;model&gt; at &lt;effort&gt;. Anything else,
# such as an effort alone, is &quot;?&quot;: it would start a session that fails at its
# first request.
efforts=&#x27;^(low|medium|high|xhigh|max)$&#x27;
lead_flags=()
lead_says=&quot;the saved model and effort&quot;
lead=&quot;$(control Lead)&quot;
if [[ -n &quot;$lead&quot; &amp;&amp; &quot;$lead&quot; != default ]]; then
  parts=($&#123;=lead&#125;)
  [[ &quot;$&#123;parts[2]:-&#125;&quot; == at ]] &amp;&amp; parts[2]=()
  if (( $&#123;#parts&#125; &gt;= 1 &amp;&amp; $&#123;#parts&#125; &lt;= 2 )) &amp;&amp; [[ &quot;$&#123;parts[1]&#125;&quot; =~ &#x27;^[A-Za-z0-9.-]+$&#x27; &amp;&amp; ! &quot;$&#123;parts[1]&#125;&quot; =~ $efforts ]] \
     &amp;&amp; [[ $&#123;#parts&#125; == 1 || &quot;$&#123;parts[2]&#125;&quot; =~ $efforts ]]; then
    lead_flags+=(--model &quot;$&#123;parts[1]&#125;&quot;)
    lead_says=&quot;$&#123;parts[1]&#125;, saved effort&quot;
    if (( $&#123;#parts&#125; == 2 )); then lead_flags+=(--effort &quot;$&#123;parts[2]&#125;&quot;); lead_says=&quot;$&#123;parts[1]&#125; at $&#123;parts[2]&#125;&quot;; fi
  else
    lead_says=&quot;?&quot;
  fi
fi
unclear_lead=&#x27;roadmap.md&#x27;&quot;&#x27;&quot;&#x27;s Lead line can&#x27;&quot;&#x27;&quot;&#x27;t be read: nothing starts. Write **default**, a model (**opus**), or a model and an effort (**opus high**).&#x27;

# Who pays: the Controls&#x27; Billing line. &quot;plan&quot; (no such line, or **plan**)
# bills Orlando&#x27;s plan, with Remote Control on. &quot;sdk&quot; runs the lead through
# the Agent SDK (sdk-run.py, beside this script) on his API key, which his
# plan&#x27;s promotional credit covers, without Remote Control, and stopped at
# the Controls&#x27; Lead budget. Claude Code reads the key from the Keychain
# itself, through the apiKeyHelper sdk-run.py sets; this script never reads
# it. Each lead starts the next with this script, so the choice holds until
# the line changes. &quot;api&quot; (an interactive claude with an apiKeyHelper) is
# refused: it is billed as Claude Code, which the credit doesn&#x27;t cover, and
# it once drained his paid balance until every call was refused.
# &quot;?&quot; for a line that is there but not in a known form, or more than one:
# nothing starts, rather than bill the wrong account.
billing=$(awk &quot;$awk_lib&quot;&#x27;
  fenceline($0) &#123; next &#125;
  fence &#123; next &#125;
  /^## / &#123; sec = (!seen &amp;&amp; $0 ~ /^## Controls([ \t]|$)/); if (sec) seen = 1; next &#125;
  sec &amp;&amp; tolower($0) ~ /^[ \t]*([-*+][ \t]*)?(\*\*)?[ \t]*billing/ &#123; n++; line = $0 &#125;
  END &#123;
    if (!n) &#123; print &quot;plan&quot;; exit &#125;
    if (n &gt; 1) &#123; print &quot;?&quot;; exit &#125;
    if (line ~ /^- Billing: \*\*plan\*\*/) &#123; print &quot;plan&quot;; exit &#125;
    if (line ~ /^- Billing: \*\*sdk\*\*/) &#123; print &quot;sdk&quot;; exit &#125;
    if (line ~ /^- Billing: \*\*api\*\*/) &#123; print &quot;api&quot;; exit &#125;
    print &quot;?&quot;
  &#125;
&#x27; &quot;$roadmap&quot;)
unclear_billing=&#x27;roadmap.md&#x27;&quot;&#x27;&quot;&#x27;s Billing line can&#x27;&quot;&#x27;&quot;&#x27;t be read: nothing starts. Write &quot;- Billing: **plan**&quot; or &quot;- Billing: **sdk**&quot;, once, or leave the line out for the plan.&#x27;
refused_api=&#x27;roadmap.md says &quot;- Billing: **api**&quot;, which is refused: nothing starts. It bills Claude Code to the API key, which the credit does not cover. Write &quot;- Billing: **sdk**&quot; instead.&#x27;

# The most an sdk lead may spend, in US dollars: the Controls&#x27; &quot;- Lead
# budget: **&lt;amount&gt;**&quot; line, a default with no such line. &quot;?&quot; for a line that is there
# but not a plain number above 0, or more than one: an sdk lead doesn&#x27;t
# start without a cap it can read.
budget=$(awk &quot;$awk_lib&quot;&#x27;
  fenceline($0) &#123; next &#125;
  fence &#123; next &#125;
  /^## / &#123; sec = (!seen &amp;&amp; $0 ~ /^## Controls([ \t]|$)/); if (sec) seen = 1; next &#125;
  sec &amp;&amp; tolower($0) ~ /^[ \t]*([-*+][ \t]*)?(\*\*)?[ \t]*lead[ \t]+budget/ &#123; n++; line = $0 &#125;
  END &#123;
    if (!n) &#123; print &quot;&lt;default-budget&gt;&quot;; exit &#125;
    if (n &gt; 1) &#123; print &quot;?&quot;; exit &#125;
    if (line ~ /^- Lead budget: \*\*\$?[0-9]+(\.[0-9]+)?\*\*/) &#123; v = line; sub(/^- Lead budget: \*\*\$?/, &quot;&quot;, v); sub(/\*\*.*/, &quot;&quot;, v); if (v + 0 &gt; 0) &#123; print v; exit &#125; &#125;
    print &quot;?&quot;
  &#125;
&#x27; &quot;$roadmap&quot;)
unclear_budget=&#x27;roadmap.md&#x27;&quot;&#x27;&quot;&#x27;s Lead budget line can&#x27;&quot;&#x27;&quot;&#x27;t be read: nothing starts on sdk. Write it as &quot;- Lead budget: **&lt;amount&gt;**&quot; (US dollars, above 0), once, or leave it out for the default.&#x27;
case &quot;$billing&quot; in
  sdk)  billing_says=&quot;the API key through the Agent SDK, without Remote Control, at most \$$budget a lead&quot; ;;
  plan) billing_says=&quot;the plan, with Remote Control&quot; ;;
  api)  billing_says=&quot;api, refused (use sdk)&quot; ;;
  *)    billing_says=&quot;?&quot; ;;
esac
# How a lead&#x27;s process is told apart, by its command line. On the plan:
# &quot;claude --remote-control &lt;its name&gt; ...&quot;. An older lead on the key: &quot;claude
# --name &lt;its name&gt; ...&quot;. On sdk: &quot;uv run &lt;this folder&gt;/sdk-run.py ...
# --label &lt;its name&gt; ...&quot;, the uv process being the pane&#x27;s own program. Each
# is followed by &quot;&lt;Name&gt;: Batch N&quot;. Two groups: the batch number is \3.
lead_re=&#x27;^([^ ]*claude --(remote-control|name) |[^ ]*uv run [^ ]*sdk-run\.py .*--label )&#x27;

state=&quot;$&#123;found%% *&#125;&quot;
batch=$(print -r -- &quot;$&#123;found#* &#125;&quot; | sed -E &#x27;s/^### (Batch [0-9]+).*/\1/&#x27;)
number=&quot;$&#123;batch#Batch &#125;&quot;
sess=&quot;$name: $batch&quot;

# This script&#x27;s own ancestors: a lead that calls it is one of them.
ancestors=()
pid=$$
while [[ -n &quot;$pid&quot; &amp;&amp; &quot;$pid&quot; != &quot;1&quot; &amp;&amp; &quot;$pid&quot; != &quot;0&quot; ]]; do
  ancestors+=(&quot;$pid&quot;)
  pid=$(ps -o ppid= -p &quot;$pid&quot; 2&gt;/dev/null | tr -d &#x27; &#x27;)
done
# The status word of a batch, by its number: queued, running, done.
status_of() &#123;
  awk -v n=&quot;$1&quot; &quot;$awk_lib&quot;&#x27;
    fenceline($0) &#123; on = 0; next &#125;
    fence &#123; next &#125;
    /^[ \t]*&gt;/ &#123; on = 0; next &#125;
    /^## / &#123; batches = (!seen &amp;&amp; $0 ~ /^## Batches([ \t]|$)/); if (batches) seen = 1; on = 0; next &#125;
    &#123; if (on &amp;&amp; $0 ~ /^Status: /) &#123; s = $0; sub(/^Status: \*\*/, &quot;&quot;, s); sub(/\*\*.*/, &quot;&quot;, s); print s; exit &#125;
      on = (batches &amp;&amp; $0 ~ (&quot;^### Batch &quot; n &quot;([^0-9]|$)&quot;)) &#125;
  &#x27; &quot;$roadmap&quot;
&#125;
# Is a lead of this relay the one calling? Then which batch is its own, and
# whether that batch is done. A lead whose batch is done has ended its work,
# so its pane is put away below whatever else happens.
caller_batch=&quot;&quot;
for a in &quot;$&#123;ancestors[@]&#125;&quot;; do
  n=$(ps -o args= -p &quot;$a&quot; 2&gt;/dev/null | sed -n -E &quot;s/$&#123;lead_re&#125;$name: Batch ([0-9]+)([^0-9].*)?$/\3/p&quot; | head -1)
  if [[ -n &quot;$n&quot; ]]; then caller_batch=&quot;$n&quot;; fi
done
caller_done=0
if [[ -n &quot;$caller_batch&quot; &amp;&amp; &quot;$(status_of &quot;$caller_batch&quot;)&quot; == done ]]; then caller_done=1; fi
# Other lead sessions of this project. One whose batch is marked done has
# finished and is only an open pane, waiting to be put away: it is not
# leading anything, and doesn&#x27;t count.
others=()
finished=()
finished_pids=()
for other in $(pgrep -f &quot;$&#123;lead_re&#125;$name: Batch [0-9]+&quot; || true); do
  (( $&#123;ancestors[(Ie)$other]&#125; )) &amp;&amp; continue
  n=$(ps -o args= -p &quot;$other&quot; 2&gt;/dev/null | sed -n -E &quot;s/$&#123;lead_re&#125;$name: Batch ([0-9]+)([^0-9].*)?$/\3/p&quot; | head -1)
  if [[ -n &quot;$n&quot; &amp;&amp; &quot;$(status_of &quot;$n&quot;)&quot; == done ]]; then
    finished+=(&quot;Batch $n&quot;)
    finished_pids+=(&quot;$other&quot;)
  else
    others+=(&quot;$other&quot;)
  fi
done

rex_bin=$(command -v rex || true)
[[ -n &quot;$rex_bin&quot; ]] || rex_bin=&quot;$HOME/Library/Application Support/com.superlogical.rex/RexServer/Versions/Current/Rex Server.app/Contents/MacOS/rex&quot;
# jq (part of macOS) reads what Rex says about its panes. Without it sessions
# still start, but their tab isn&#x27;t named and stray finished panes aren&#x27;t found.
jq_bin=$(command -v jq || true)
# How long a finished pane must be idle before it closes. Tests shorten it.
close_after=&quot;$&#123;RELAY_CLOSE_AFTER:-900&#125;&quot;

# pane_of &lt;process id&gt;: the Rex pane that process runs in. Rex knows each
# pane&#x27;s program by its process id. Sets REPLY to the pane&#x27;s block id.
pane_of() &#123;
  REPLY=&quot;&quot;
  [[ -x &quot;$rex_bin&quot; &amp;&amp; -n &quot;$jq_bin&quot; ]] || return 1
  local s b p
  for s in $(&quot;$rex_bin&quot; ls --json --autostart=false 2&gt;/dev/null | &quot;$jq_bin&quot; -r &#x27;.sessions[]?.session_id&#x27; 2&gt;/dev/null); do
    for b in $(&quot;$rex_bin&quot; block ls -s &quot;$s&quot; --json --autostart=false 2&gt;/dev/null | &quot;$jq_bin&quot; -r &#x27;.blocks[]?.block_id&#x27; 2&gt;/dev/null); do
      p=$(&quot;$rex_bin&quot; block call -b &quot;$b&quot; process --autostart=false 2&gt;/dev/null | &quot;$jq_bin&quot; -r &#x27;.child.pid // empty&#x27; 2&gt;/dev/null) || p=&quot;&quot;
      if [[ &quot;$p&quot; == &quot;$1&quot; ]]; then REPLY=&quot;$b&quot;; return 0; fi
    done
  done
  return 1
&#125;

# pane_state &lt;process id&gt;: what that lead&#x27;s pane reports, for --status:
# &quot;working&quot;, &quot;idle for 2 h 10 min&quot;, or nothing when it can&#x27;t be read. Read the
# same way close-pane-later.sh reads it. Sets REPLY, and LEAD_IDLE to the idle
# seconds (0 when working or unknown).
pane_state() &#123;
  REPLY=&quot;&quot; LEAD_IDLE=0
  pane_of &quot;$1&quot; || return 1
  local st count working last at idle
  st=$(&quot;$rex_bin&quot; block call -b &quot;$REPLY&quot; program_status --autostart=false 2&gt;/dev/null) || &#123; REPLY=&quot;&quot;; return 1; &#125;
  REPLY=&quot;&quot;
  print -r -- &quot;$st&quot; | &quot;$jq_bin&quot; -r &#x27;[.records[]?] as $r | &quot;\($r | length) \([$r[] | select(.state == &quot;working&quot;)] | length) \([$r[] | .updated_at // empty] | max // &quot;&quot;)&quot;&#x27; 2&gt;/dev/null | read -r count working last
  [[ &quot;$count&quot; == &lt;-&gt; ]] &amp;&amp; (( count &gt; 0 )) || return 1
  if (( working &gt; 0 )); then REPLY=&quot;working&quot;; return 0; fi
  zmodload zsh/datetime
  [[ -n &quot;$last&quot; ]] &amp;&amp; strftime -r -s at &#x27;%Y-%m-%dT%H:%M:%S&#x27; &quot;$&#123;last[1,19]&#125;&quot; 2&gt;/dev/null || return 1
  idle=$(( EPOCHSECONDS - at ))
  (( idle &gt;= 0 )) || return 1
  LEAD_IDLE=$idle
  if (( idle &gt;= 3600 )); then REPLY=&quot;idle for $(( idle / 3600 )) h $(( idle % 3600 / 60 )) min&quot;
  else REPLY=&quot;idle for $(( idle / 60 )) min&quot;; fi
  return 0
&#125;

if [[ &quot;$mode&quot; == status ]]; then
  echo &quot;$name ($project)&quot;
  awk &quot;$awk_lib&quot;&#x27;
    fenceline($0) &#123; next &#125;
    fence &#123; next &#125;
    /^## / &#123; sec = (!seen &amp;&amp; $0 ~ /^## Controls([ \t]|$)/); if (sec) seen = 1; next &#125;
    sec &amp;&amp; /^- (Relay|Stop after batch|Agents|Lead):/ &#123; s = $0; sub(/^- /, &quot;  &quot;, s); gsub(/\*\*/, &quot;&quot;, s); print s &#125;
  &#x27; &quot;$roadmap&quot;
  if [[ &quot;$billing&quot; == &quot;?&quot; ]]; then echo &quot;  Billing: can&#x27;t be read&quot;; else echo &quot;  Billing: $billing_says&quot;; fi
  echo &quot;Batches:&quot;
  awk &quot;$awk_lib&quot;&#x27;
    fenceline($0) &#123; name = &quot;&quot;; next &#125;
    fence &#123; next &#125;
    /^[ \t]*&gt;/ &#123; name = &quot;&quot;; next &#125;
    /^## / &#123; batches = (!seen &amp;&amp; $0 ~ /^## Batches([ \t]|$)/); if (batches) seen = 1; name = &quot;&quot;; next &#125;
    &#123; if (name != &quot;&quot; &amp;&amp; $0 ~ /^Status: /) &#123; s = $0; gsub(/\*\*/, &quot;&quot;, s); sub(/^Status: /, &quot;&quot;, s); print &quot;  &quot; name &quot;: &quot; s &#125;
      name = (batches &amp;&amp; $0 ~ /^### Batch [0-9]+/) ? substr($0, 5) : &quot;&quot; &#125;
  &#x27; &quot;$roadmap&quot;
  if (( $&#123;#others&#125; )); then
    # Each live lead, with what its pane reports. A lead stopped by a usage
    # limit in the middle of a turn stays alive and idle, and blocks every
    # restart until it is told to carry on or its pane is closed.
    running=() long_idle=0
    for o in &quot;$&#123;others[@]&#125;&quot;; do
      r=$(ps -o args= -p &quot;$o&quot; 2&gt;/dev/null | sed -n -E &quot;s/$&#123;lead_re&#125;($name: Batch [0-9]+)([^0-9].*)?$/\3/p&quot; | head -1)
      [[ -n &quot;$r&quot; ]] || r=&quot;process $o&quot;
      if pane_state &quot;$o&quot; &amp;&amp; [[ -n &quot;$REPLY&quot; ]]; then r=&quot;$r ($REPLY)&quot;; (( LEAD_IDLE &gt;= 3600 )) &amp;&amp; long_idle=1; fi
      running+=(&quot;$r&quot;)
    done
    echo &quot;A lead session is running: $&#123;(j:, :)running&#125;&quot;
    if (( long_idle )); then echo &quot;A lead idle this long may have stopped at a usage limit, and blocks every restart. See \&quot;After a crash, a power cut or a usage limit\&quot; in the relay skill.&quot;; fi
  else
    echo &quot;No lead session is running.&quot;
  fi
  if (( $&#123;#finished&#125; )); then echo &quot;Finished, with its pane still open: $&#123;(j:, :)finished&#125;&quot;; fi
  if [[ -n &quot;$found&quot; ]]; then echo &quot;Next to lead: $batch ($state)&quot;; else echo &quot;Nothing is running or queued.&quot;; fi
  if [[ &quot;$(relay_state)&quot; == unclear ]]; then echo &quot;$unclear_relay&quot;; fi
  [[ -n &quot;$unreadable_batches&quot; ]] &amp;&amp; echo &quot;$unreadable_batches&quot;
  [[ &quot;$stop_after&quot; == &quot;?&quot; ]] &amp;&amp; echo &quot;$unclear_stop&quot;
  if [[ &quot;$lead_says&quot; == &quot;?&quot; ]]; then echo &quot;$unclear_lead&quot;; else echo &quot;A new lead starts on: $lead_says.&quot;; fi
  [[ &quot;$billing&quot; == &quot;?&quot; ]] &amp;&amp; echo &quot;$unclear_billing&quot;
  [[ &quot;$billing&quot; == api ]] &amp;&amp; echo &quot;$refused_api&quot;
  [[ &quot;$billing&quot; == sdk &amp;&amp; &quot;$budget&quot; == &quot;?&quot; ]] &amp;&amp; echo &quot;$unclear_budget&quot;
  if pgrep -f &#x27;memory-fuse\.sh watch$&#x27; &gt;/dev/null 2&gt;&amp;1; then
    echo &quot;The memory fuse is watching.&quot;
  else
    echo &quot;The memory fuse is not watching. It starts with the next lead.&quot;
  fi
  exit 0
fi

# close_later &lt;block id&gt;: closes that pane once its session has been idle
# long enough. The timer runs on its own, in no pane, one to a pane.
close_later() &#123;
  if pgrep -f &quot;close-pane-later.sh $1 &quot; &gt;/dev/null 2&gt;&amp;1; then return 0; fi
  ( &quot;$here/close-pane-later.sh&quot; &quot;$1&quot; &quot;$close_after&quot; &lt;/dev/null &gt;/dev/null 2&gt;&amp;1 &amp; )
&#125;

# Finished leads whose panes are still open (a lead that ended without
# running this script, or whose timer was lost) are put away too.
sweep_finished() &#123;
  local p
  for p in &quot;$&#123;finished_pids[@]&#125;&quot;; do
    if pane_of &quot;$p&quot; &amp;&amp; [[ &quot;$REPLY&quot; != &quot;$REX_BLOCK&quot; ]]; then close_later &quot;$REPLY&quot;; fi
  done
  return 0
&#125;

# The pane this was run from, when a lead whose batch is done is the one
# calling, or --close-me was given.
close_caller() &#123;
  [[ &quot;$mode&quot; == start &amp;&amp; -n &quot;$REX_BLOCK&quot; &amp;&amp; -x &quot;$rex_bin&quot; ]] || return 0
  (( close_me || caller_done )) || return 0
  close_later &quot;$REX_BLOCK&quot;
  echo &quot;This session&#x27;s pane closes by itself once it has been idle for $(( (close_after + 59) / 60 )) minutes. Write your last message to Orlando now, and do nothing else afterwards.&quot;
&#125;

# What a real run ends with, whether or not anything was started.
put_away() &#123;
  [[ &quot;$mode&quot; == start ]] || return 0
  sweep_finished
  close_caller
&#125;

if [[ -n &quot;$unreadable_batches&quot; ]]; then
  echo &quot;$unreadable_batches&quot;
  put_away
  exit 0
fi
if [[ -z &quot;$found&quot; ]]; then
  echo &quot;No batch is running or queued in roadmap.md: nothing to start.&quot;
  put_away
  exit 0
fi
case &quot;$(relay_state)&quot; in
  on) ;;
  unclear)
    echo &quot;$unclear_relay&quot;
    put_away
    exit 0 ;;
  *)
    echo &quot;roadmap.md says the relay is off: nothing started.&quot;
    put_away
    exit 0 ;;
esac
if [[ &quot;$stop_after&quot; == &quot;?&quot; ]]; then
  echo &quot;$unclear_stop&quot;
  put_away
  exit 0
fi
if [[ &quot;$state&quot; == queued &amp;&amp; &quot;$stop_after&quot; == &lt;-&gt; &amp;&amp; &quot;$number&quot; -gt &quot;$stop_after&quot; ]]; then
  echo &quot;roadmap.md says to stop after batch $stop_after: nothing started.&quot;
  put_away
  exit 0
fi
# Never two lead sessions for one project at once. The session that has just
# finished the batch before is still running when it calls this: it is one of
# this script&#x27;s ancestors, and doesn&#x27;t count.
if (( $&#123;#others&#125; )); then
  echo &quot;Another lead session for $name is already running (process $&#123;others[1]&#125;): nothing started.&quot;
  close_caller
  exit 0
fi
# Nor a second session for a batch whose own lead is the one calling: it
# marks its batch done before it starts the next. With --close-me it is
# stopping part way, and only wants its pane put away.
if [[ &quot;$state&quot; == running &amp;&amp; -n &quot;$caller_batch&quot; &amp;&amp; &quot;$caller_batch&quot; == &quot;$number&quot; ]]; then
  if (( close_me )); then
    echo &quot;$batch is marked running and this session is leading it: nothing started. The next start carries on from what is in the files.&quot;
    close_caller
  else
    echo &quot;$batch is marked running and this session is leading it: nothing started. Mark it done in roadmap.md first.&quot;
  fi
  exit 0
fi

if [[ &quot;$lead_says&quot; == &quot;?&quot; ]]; then
  echo &quot;$unclear_lead&quot;
  put_away
  exit 0
fi
if [[ &quot;$billing&quot; == &quot;?&quot; ]]; then
  echo &quot;$unclear_billing&quot;
  put_away
  exit 0
fi
if [[ &quot;$billing&quot; == api ]]; then
  echo &quot;$refused_api&quot;
  put_away
  exit 0
fi
if [[ &quot;$billing&quot; == sdk &amp;&amp; &quot;$budget&quot; == &quot;?&quot; ]]; then
  echo &quot;$unclear_budget&quot;
  put_away
  exit 0
fi

# RELAY_TEST_CLAUDE puts a stand-in in claude&#x27;s place, for this script&#x27;s own tests.
claude_bin=&quot;$&#123;RELAY_TEST_CLAUDE:-$HOME/.local/bin/claude&#125;&quot;
[[ -x &quot;$claude_bin&quot; ]] || claude_bin=$(command -v claude || true)
[[ -n &quot;$claude_bin&quot; ]] || &#123; echo &quot;Can&#x27;t find the claude command.&quot; &gt;&amp;2; exit 1; &#125;
[[ -x &quot;$rex_bin&quot; ]] || &#123; echo &quot;Can&#x27;t find rex: the relay starts its sessions in Rex panes.&quot; &gt;&amp;2; exit 1; &#125;

# The prompt is one plain line, so no shell on the way can mangle it.
prompt=&quot;Read $skill/lead.md and do what it says.&quot;
carrying=&quot;&quot;
[[ &quot;$state&quot; == running ]] &amp;&amp; carrying=&quot; It carries on from where the stopped session left $batch.&quot;
opening=(&quot;$rex_bin&quot; run --json --focus=false --label &quot;$sess&quot; -c &quot;$project&quot;)
# lead-settings.json turns a permission question into a &quot;no&quot;, so a lead never
# waits all night on one. Leads only: his own
# sessions still ask.
session_cmd=(-- &quot;$claude_bin&quot; --remote-control &quot;$sess&quot; --permission-mode auto --settings &quot;$skill/lead-settings.json&quot; &quot;$&#123;lead_flags[@]&#125;&quot; &quot;$prompt&quot;)
# On sdk: the same first message, model, effort, settings file and
# permission mode, through sdk-run.py, which may read this skill&#x27;s folder
# (lead.md, the briefs) without asking, and loads the user&#x27;s, project&#x27;s and
# local settings too (their deny rules hold) and stops at the budget. The
# lead&#x27;s agents run in its process, so they bill the key too. A lead stopped
# by its budget gets an entry in roadmap.md&#x27;s Log from sdk-run.py.
# RELAY_TEST_UV puts a stand-in in uv&#x27;s place, for this script&#x27;s own tests.
if [[ &quot;$billing&quot; == sdk ]]; then
  uv_bin=&quot;$&#123;RELAY_TEST_UV:-$(command -v uv || true)&#125;&quot;
  [[ -n &quot;$uv_bin&quot; ]] || uv_bin=&quot;$HOME/.local/bin/uv&quot;
  [[ -x &quot;$uv_bin&quot; ]] || &#123; echo &quot;Can&#x27;t find uv: an sdk lead runs with uv run.&quot; &gt;&amp;2; exit 1; &#125;
  session_cmd=(-- &quot;$uv_bin&quot; run &quot;$here/sdk-run.py&quot; --cwd &quot;$project&quot; --budget &quot;$budget&quot; --settings &quot;$skill/lead-settings.json&quot; --read-dir &quot;$skill&quot; --roadmap &quot;$roadmap&quot; &quot;$&#123;lead_flags[@]&#125;&quot; --label &quot;$sess&quot; --prompt &quot;$prompt&quot;)
fi

if [[ &quot;$mode&quot; == dry-run ]]; then
  echo &quot;Would start: $sess.$carrying&quot;
  echo &quot;On: $lead_says.&quot;
  [[ &quot;$billing&quot; == sdk ]] &amp;&amp; echo &quot;Billing: $billing_says.&quot;
  printf &#x27;%q &#x27; &quot;$&#123;opening[@]&#125;&quot; $&#123;REX_SESSION:+-s&#125; $&#123;REX_SESSION:+&quot;$REX_SESSION&quot;&#125; &quot;$&#123;session_cmd[@]&#125;&quot;; echo
  exit 0
fi

# Where the new pane goes. Beside the lead that is handing over (the caller),
# or beside a finished lead&#x27;s pane that is still open, so the relay keeps its
# tab. With neither, a new tab: in this Rex session if there is one, in Rex&#x27;s
# default place otherwise.
anchor=&quot;&quot;
if [[ -n &quot;$caller_batch&quot; &amp;&amp; -n &quot;$REX_BLOCK&quot; ]]; then
  anchor=&quot;$REX_BLOCK&quot;
else
  for p in &quot;$&#123;finished_pids[@]&#125;&quot;; do
    if pane_of &quot;$p&quot;; then anchor=&quot;$REPLY&quot;; break; fi
  done
fi
out=&quot;&quot;
started=0
if [[ -n &quot;$anchor&quot; ]]; then
  if out=$(&quot;$&#123;opening[@]&#125;&quot; --split=right -b &quot;$anchor&quot; &quot;$&#123;session_cmd[@]&#125;&quot; 2&gt;&amp;1); then started=1; fi
fi
if (( ! started )); then
  if [[ -n &quot;$REX_SESSION&quot; ]] &amp;&amp; out=$(&quot;$&#123;opening[@]&#125;&quot; -s &quot;$REX_SESSION&quot; &quot;$&#123;session_cmd[@]&#125;&quot; 2&gt;&amp;1); then
    :
  elif out=$(&quot;$&#123;opening[@]&#125;&quot; &quot;$&#123;session_cmd[@]&#125;&quot; 2&gt;&amp;1); then
    :
  else
    echo &quot;Rex could not start the session: $out&quot; &gt;&amp;2
    exit 1
  fi
fi
# The new pane&#x27;s id. A split&#x27;s answer also names the pane it was split from
# (its anchor), which is not the one wanted.
block=&quot;&quot;
if [[ -n &quot;$jq_bin&quot; ]]; then
  block=$(print -r -- &quot;$out&quot; | &quot;$jq_bin&quot; -r &#x27;.block_ids[0] // empty&#x27; 2&gt;/dev/null) || block=&quot;&quot;
fi
if [[ -z &quot;$block&quot; ]]; then
  block=$(print -r -- &quot;$out&quot; | grep -v &#x27;&quot;anchor_id&quot;&#x27; | sed -n -E &#x27;s/.*&quot;(block:[^&quot;]+)&quot;.*/\1/p&#x27; | head -1)
fi

# Name the relay&#x27;s tab after the session leading now, so Orlando can tell his
# tabs apart. A name he gave the tab himself is left alone.
name_tab() &#123;
  [[ -n &quot;$jq_bin&quot; &amp;&amp; -n &quot;$block&quot; ]] || return 0
  local info s w label
  info=$(&quot;$rex_bin&quot; block inspect &quot;$block&quot; --json 2&gt;/dev/null) || return 0
  s=$(print -r -- &quot;$info&quot; | &quot;$jq_bin&quot; -r &#x27;.session_id // empty&#x27; 2&gt;/dev/null) || return 0
  w=$(print -r -- &quot;$info&quot; | &quot;$jq_bin&quot; -r &#x27;.block.window_id // empty&#x27; 2&gt;/dev/null) || return 0
  [[ -n &quot;$s&quot; &amp;&amp; -n &quot;$w&quot; ]] || return 0
  label=$(&quot;$rex_bin&quot; window ls -s &quot;$s&quot; --json 2&gt;/dev/null | &quot;$jq_bin&quot; -r --arg w &quot;$w&quot; &#x27;.windows[]? | select(.window_id == $w) | .label // empty&#x27; 2&gt;/dev/null) || label=&quot;&quot;
  if [[ -z &quot;$label&quot; || &quot;$label&quot; =~ &#x27;^Window [0-9a-f]+$&#x27; || &quot;$label&quot; == &quot;$name: Batch &quot;* ]]; then
    &quot;$rex_bin&quot; window rename &quot;$w&quot; &quot;$sess&quot; -s &quot;$s&quot; &gt;/dev/null 2&gt;&amp;1 || true
  fi
  return 0
&#125;
name_tab

# Did it come up? Its process should be there within a few seconds.
up=0
for i in &#123;1..20&#125;; do
  for p in $(pgrep -f &quot;$&#123;lead_re&#125;$sess( |\$)&quot; || true); do
    (( $&#123;ancestors[(Ie)$p]&#125; )) || up=1
  done
  (( up )) &amp;&amp; break
  sleep 1
done
if (( ! up )); then
  echo &quot;The session for $batch did not come up. Look at its pane: rex capture -b $block&quot; &gt;&amp;2
  exit 1
fi
if [[ &quot;$billing&quot; == sdk ]]; then
  echo &quot;Started a fresh session for $batch through the Agent SDK, billed to the API key, at most \$$budget. It is in this relay&#x27;s tab in Rex as \&quot;$sess\&quot;, and not in the Claude app: Remote Control needs the plan. Its cost goes to ~/.claude/sdk-costs.log.$carrying&quot;
else
  echo &quot;Started a fresh session for $batch. It is in this relay&#x27;s tab in Rex and in the Claude app, both as \&quot;$sess\&quot;.$carrying&quot;
fi
echo &quot;Read its pane with: rex capture -b $block&quot;

# The memory fuse: one for the whole Mac while any relay lead runs. It stops a
# single runaway process that a lead or its agents started, before macOS runs
# out of memory. It runs on its own, in no pane, and ends by itself once no
# lead is left. A second one ends at once. A run with a stand-in for claude
# (this script&#x27;s own tests) starts none.
if [[ -z &quot;$&#123;RELAY_TEST_CLAUDE:-&#125;&quot; &amp;&amp; -x &quot;$here/memory-fuse.sh&quot; ]]; then
  ( &quot;$here/memory-fuse.sh&quot; watch &lt;/dev/null &gt;/dev/null 2&gt;&amp;1 &amp; )
fi

# Put the finished sessions away, so old ones don&#x27;t pile up for Orlando to
# close by hand. What each did is in roadmap.md&#x27;s Log and in git.
put_away
</code></pre>

### <code>watch&#45;relay&#46;zsh</code> {#watch-relay-zsh}

<a href="#watch-relay-zsh">Link to this file</a>

Watches one relay from another session and exits with an "EVENT:" line when a batch finishes, no lead is running, the relay's sessions go quiet for 30 minutes, or a time limit passes. It costs nothing while it waits.

**Needs to run:** macOS, and the relay's <code>start&#45;lead&#46;sh</code> script.

<pre><code style="white-space: pre-wrap; word-break: normal; overflow-wrap: anywhere">#!/bin/zsh
# Watches one relay and exits when something needs a look. It costs nothing while it waits.
# Usage: watch-relay.zsh &lt;project folder&gt; [minutes before it stops waiting]
# It prints a line starting &quot;EVENT:&quot; and exits when:
#   - a batch has been marked done since it started watching
#   - no lead session is running
#   - the relay&#x27;s sessions have written nothing for 30 minutes
#   - the time limit passes
emulate -L zsh
repo=$&#123;1:?project folder&#125;
limit_min=$&#123;2:-300&#125;
script=$HOME/.claude/skills/relay/scripts/start-lead.sh
projects=$HOME/.claude/projects
pattern=&quot;*$&#123;repo:h:t&#125;-$&#123;repo:t&#125;*&quot;

relay_status() &#123; &quot;$script&quot; &quot;$repo&quot; --status 2&gt;&amp;1 &#125;
count_done() &#123; print -r -- &quot;$1&quot; | grep -c &#x27;: done&#x27; &#125;
session_dirs() &#123; find &quot;$projects&quot; -maxdepth 1 -type d -name &quot;$pattern&quot; &#125;

start=$(date +%s)
first=$(relay_status)
done_start=$(count_done &quot;$first&quot;)
watch_quiet=yes
[[ -z &quot;$(session_dirs)&quot; ]] &amp;&amp; watch_quiet=no
print -r -- &quot;Watching since $(date &#x27;+%a %H:%M&#x27;). Batches done at start: $done_start. Quiet check: $watch_quiet.&quot;

while true; do
  sleep 300
  out=$(relay_status)
  stamp=$(date &#x27;+%a %H:%M&#x27;)
  if (( $(count_done &quot;$out&quot;) &gt; done_start )); then
    print -r -- &quot;EVENT: a batch finished ($stamp)&quot;; print -r -- &quot;$out&quot;; exit 0
  fi
  if ! print -r -- &quot;$out&quot; | grep -q &#x27;A lead session is running&#x27;; then
    print -r -- &quot;EVENT: no lead session is running ($stamp)&quot;; print -r -- &quot;$out&quot;; exit 0
  fi
  if [[ $watch_quiet == yes ]]; then
    recent=$(session_dirs | while read -r d; do find &quot;$d&quot; -type f -mmin -30 -print -quit; done 2&gt;&amp;1 | head -1)
    if [[ -z &quot;$recent&quot; ]]; then
      print -r -- &quot;EVENT: the relay&#x27;s sessions have written nothing for 30 minutes ($stamp)&quot;; print -r -- &quot;$out&quot;; exit 0
    fi
  fi
  if (( ( $(date +%s) - start ) / 60 &gt;= limit_min )); then
    print -r -- &quot;EVENT: time limit of $limit_min minutes ($stamp)&quot;; print -r -- &quot;$out&quot;; exit 0
  fi
done
</code></pre>
