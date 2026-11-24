---
title: "Relay"
date: 2026-11-22
lastmod: 2026-11-22
description: "AI-written: a skill that runs a project's roadmap as an unattended relay of fresh Claude Code sessions."
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

Each session leads one batch with parallel builder agents, has a fresh reviewer check the result, delivers and writes it up, then starts the next session.

**Works with:** <a href="/ai-setup/apps/#corillo">Corillo</a>, <a href="/ai-setup/skills/heartbeat/">Heartbeat</a>, and <a href="/ai-setup/skills/rex/">Rex</a>.

## <code>SKILL&#46;md</code>

The main instructions for the relay skill: what a relay is, when it fits, how to set one up, and how to start, check, pause, stop, and recover it.

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

It doesn&#x27;t fit work that needs his judgment at each step (writing in his voice, a money decision), or anything one session can finish. For &quot;check on something every few minutes&quot;, `/loop` is the tool.

## Set one up

1. **Agree the plan with Orlando first.** The roadmap is his: what gets built, in what order, and what is left out. If no plan exists, make one with him (one question per message), or from reviews he has asked for. Never queue work he hasn&#x27;t agreed to.
2. **Write `roadmap.md`** at the project&#x27;s root from `&lt;skill&gt;/roadmap-template.md`. What makes a roadmap run well unattended:
   - Each item says what the person gets, how big it is, and which files are its own, so two builders never edit the same file.
   - Items in one batch are independent. Work that builds on other work goes in a later batch.
   - Every decision that is Orlando&#x27;s is in the table with a recommendation. Anything that truly needs him (an account, a purchase, a judgment only he can make) goes under &quot;Later&quot;, or in a batch marked `Status: **queued, low**`, which the relay skips.
   - &quot;How this project runs&quot; is filled in from the project itself: find the real command for the checks and run it once, so the first lead doesn&#x27;t discover that it fails. Write down the delivery step and whether Orlando has agreed it may be done without asking.
   - **Keep the template&#x27;s form for what the scripts read.** The relay is on only when the `## Controls` section has one Relay line and it is exactly `- Relay: **on**`, with nothing after it (a note goes on a line of its own). A batch&#x27;s `Status:` is the line right under its `### Batch N` heading, in the `## Batches` section. Anything written another way reads as off or not there, and nothing starts. That is on purpose: a Log entry, a quoted line or a pasted example must never be able to switch a relay on or queue a batch (a lesson from the heartbeat&#x27;s security review). The heartbeat&#x27;s watchdog reads a roadmap the same way.
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
- **Telling a live lead something:** read its pane first (`rex capture -b &lt;pane&gt;`), and never type while it shows a permission question. Then `rex send -b &lt;pane&gt; &#x27;&lt;one short line&gt;&#x27;` and `rex send-key -b &lt;pane&gt; enter`. Say which session is writing and that Orlando did not type it. Anything longer goes in a file the lead can read, and the line points at it: once a long note typed this way arrived as only its last part, while a line of about 200 characters arrived whole. Read the pane again afterwards to see that the lead answered what you sent.

A project that already has its own launcher keeps using it until Orlando asks to move it over to this skill. Corillo does (`scripts/next-orchestrator.sh`): its leads follow the project&#x27;s own prompt under their own name, so this script would not see them and could start a second lead.

Each lead keeps the computer awake with its own `caffeinate -ims` (the display may sleep). Two relays can run at once in different projects, but they share Orlando&#x27;s usage and the computer, so start a second only when he asks.

One memory fuse watches every relay on the Mac. A single process that a lead or its agents started is stopped once it passes a quarter of the Mac&#x27;s memory. The lead gets one line in its pane, and a note goes in the fuse&#x27;s log file. It exists because a test once fed a very long answer to a shell line whose memory grows with the square of its input: one process asked for many times the Mac&#x27;s memory within seconds, and macOS raised its out-of-memory alarm over and over while most memory was free. What the fuse does not limit: many middling processes at once, the processor, and anything no relay lead started (this session, a heartbeat job).

## Who pays

The Controls&#x27; Billing line picks one of three modes. Change it only on Orlando&#x27;s word. Each lead starts the next with `start-lead.sh`, so the line holds until someone changes it. `--status` shows it, and a Billing line it can&#x27;t read starts nothing.

- **plan** (or no line): his plan, with Remote Control, as everything above describes.
- **sdk**: his API key through the Agent SDK, which his plan&#x27;s promotional credit covers (Claude Code itself is not covered). The lead runs as `uv run &lt;skill&gt;/scripts/sdk-run.py` in the relay&#x27;s Rex tab, with the same first message, model, effort, `lead-settings.json` and permission mode auto, and with user, project and local settings loaded. Its cap is the Controls&#x27; `- Lead budget: **&lt;amount&gt;**` (in US dollars, with a default when there is no line); a lead that spends it is stopped, and `sdk-run.py` adds an entry to the Log. Every run&#x27;s cost goes to a cost log file. Claude Code reads the key itself, through an `apiKeyHelper` that `sdk-run.py` passes in the run&#x27;s settings, so the key is never in the environment of the commands, hooks and MCP servers the lead starts, and auto mode stays on (the `CLAUDE_CODE_SUBPROCESS_ENV_SCRUB` route forces permission mode to default, so don&#x27;t use it). There is no Remote Control: Orlando reads the lead&#x27;s pane in Rex, or the Log. Nothing typed into its pane reaches it, so &quot;Telling a live lead something&quot; and the memory fuse&#x27;s line in its pane don&#x27;t work: to stop one, set Relay to **off** (it stops at its next hand-off) or close its pane.
- **api**: refused, nothing starts. It ran interactive `claude` with an `apiKeyHelper`, which is billed as Claude Code, not covered by the credit: it drained his paid balance until every call was refused.

The heartbeat&#x27;s relay watchdog checks the plan&#x27;s `/usage` before it starts a lead, so while the plan is at its limit it does not restart an sdk relay either: start that one by hand. It does restart an sdk lead stopped at its budget once no lead is alive, so each restart can spend another Lead budget (it starts at most 3 a day).

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

## <code>builder&#45;brief&#46;md</code>

The brief every builder agent in a relay batch reads first, covering where to work, how to work, and how to report back to the lead.

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

## <code>lead&#46;md</code>

The instructions for the lead session of one batch: what to read first, how to run the builders and the reviewer, how to deliver and write it up, and how to hand off to the next session.

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

**On the API key (sdk).** When the Controls say `- Billing: **sdk**`, you run through the Agent SDK (`&lt;skill&gt;/scripts/sdk-run.py`) on Orlando&#x27;s API key, not his plan: his plan&#x27;s promotional credit covers it, and every token is money, so the rules above count double. Your agents run in your process and bill the key too, with nothing for you to set. You have a hard cap, the Controls&#x27; `- Lead budget` (in US dollars, with a default if there is no such line): when it is spent you are stopped mid-turn with no chance to write anything, and `sdk-run.py` adds a short entry to the Log saying so. So commit finished work as you go, and keep the Log&#x27;s account of where things stand current at each step, not only at the end. You have no Remote Control (it needs the plan), and nothing typed into your pane reaches you: Orlando reads your pane in Rex and the Log. Your cost is written to a cost log file when you end; in your Log entry say that it is there. Never read, print or pass the key, and never run the key-lookup command yourself: Claude Code reads it through the `apiKeyHelper` that `sdk-run.py` sets, and the commands you run don&#x27;t see it. The next lead keeps the Billing and Lead budget lines as they are: change them only on Orlando&#x27;s word.

**When an action is denied.** You run with permission questions turned into a &quot;no&quot; (the skill&#x27;s `lead-settings.json`), so nothing waits for Orlando. When an action the batch needs is denied, don&#x27;t look for a way round it: finish what can be finished, say in the Log what was denied and why it was needed, and stop cleanly if the batch can&#x27;t be done without it.

If you are about to run out of usage, or the batch is going badly: stop cleanly. Commit what is finished and passing, write in the Log exactly where things stand, leave the status &quot;running&quot;, stop your `caffeinate`, and run `&lt;skill&gt;/scripts/start-lead.sh &lt;project folder&gt; --close-me`. That starts nothing while your batch is marked running. It puts your pane away once you have been idle for 15 minutes, so no stopped session is left open in Rex. Then write your last message and end.

The next start carries on from there, and it may come without anyone asking: if the heartbeat&#x27;s watchdog watches this relay, it starts a fresh lead once no lead is alive and the usage allows. So if a fresh lead would only meet the same trouble, also set Relay to **off** in the Controls and say why in the Log.

## Keep the computer usable

It is Orlando&#x27;s own Mac. His apps and other relays share it. A process that grows without bound can raise macOS&#x27;s out-of-memory alarm for the whole Mac within seconds, while most memory is still free: a test of a very long answer once did that over and over.

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

## <code>reviewer&#45;brief&#46;md</code>

The brief for the fresh, read-only reviewer who reads a finished batch, with what to look for and the form its findings take.

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

## <code>roadmap&#45;template&#46;md</code>

The blank form of a project's roadmap file, with its controls, decisions table, batches, and log, which the relay's scripts read.

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
- Lead budget: **&lt;amount&gt;** (in US dollars, the most one lead may spend on **sdk**; the plan ignores it)

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

Listed by name, with one line each. Their code is not shared.

- <code>close&#45;pane&#45;later&#46;sh</code>: Closes a Rex pane once the session in it has been idle for a while. This is
- <code>memory&#45;fuse&#46;sh</code>: The relay's memory fuse.
- <code>sdk&#45;run&#46;py</code>: Run one Claude Code session through the Agent SDK, billed to the API key.
- <code>start&#45;lead&#46;sh</code>: Starts a fresh Claude Code session to lead the next batch of a project's
- <code>watch&#45;relay&#46;zsh</code>: Watches one relay and exits when something needs a look. It costs nothing while it waits.

Taken out: 9 dates and incident specifics, 5 amounts and sizes, 3 log file paths, and 3 mentions of where the API key is kept.
