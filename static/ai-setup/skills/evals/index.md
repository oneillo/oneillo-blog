# Evals

- Kind: Claude Code skill
- Address: https://oneillo.com/ai-setup/skills/evals/
- Last updated: 2026-11-27

> AI-written and cleaned from one person's working setup. Placeholders in angle brackets are not real values: ask your user for theirs. The newest version is at https://oneillo.com/ai-setup/skills/evals/.

Checks that a decided change to an agent or instruction file is no worse than the current version, using a blind panel of judges.

- **What it is:** Evals is a Claude Code skill that checks a decided change against the current version before it replaces it.
- **How it starts:** You type `/evals`. Claude can also pick it up when it fits.
- **Where the file goes:** `~/.claude/skills/evals/SKILL.md`
- **Needs:** the evals tool
- **To make it yours:** Put your own values where you see `<cap>`, `<likely cost>`, `<project-folder>`, and `<top cost>`.
  Taken out for privacy: 6 amounts, 1 project folder, and 1 date.

**Works with:**
- **Called by:** [Tune setup](https://oneillo.com/ai-setup/skills/tune-setup/).

## The skill file (`SKILL.md`)

The instructions for running the eval tool, which has two versions answer the same cases and a blind panel of judges compare them. It is used when the person asks to test or compare a change they have already decided on.

````
---
name: evals
description: Checks that a change the person has decided on (to an agent, its model or effort, or an instruction file) is no worse than the current version before it replaces it. Use when they ask to test or compare a change with the eval tool; never to look for changes.
---

# Evals: check a change before it replaces the current version

The eval tool has both versions answer the same cases, a blind panel of three judges compares the answers, and the person gets one page with the verdict. Run it as `<tool folder>/bin/evals`; below it is written `evals` for short. `<tool folder>` is `~/ai/projects/ai-setup/<project-folder>/evals`, the folder that holds `bin/evals` and `README.md` (which says what each part does).

## What this skill never does

- **It never proposes a change by itself.** A check starts only because the person and a session already decided on a change. Don't suggest changes to their setup because of this skill, and don't look for things to check.
- **It never replaces anything.** Don't edit the current file. Whatever the verdict, whether the candidate replaces the current version is the person's decision, said in their own words. Until then, nothing changes.
- **No `--api`** unless they said so for this check. Without it, every call runs on their Claude plan and nothing is billed.

## Working with the person

- One question at a time.
- Ask for as little of their time as gives a trustworthy answer. Show them drafts to correct, never a blank form.
- Before the first check on one of their agents, offer once to have them read `sanity-run.md` in the tool's folder (a version against itself, and against a copy made worse on purpose). Its self check passed only with three answers a case: with one, the version lost to itself twice. Say so.

## Where their files and a check's data live

Two places hold the person's own material, so for any real agent neither goes into the tool's folder:

- **The case file**, one per agent, kept for the next check of the same agent (step 2).
- **The data folder:** checks, kept answers and ranking sheets hold the tasks, the rubric, the voice sample and the agent's answers. Set `EVALS_DATA` to a folder outside the tool's folder on every `evals` command, such as `evals-data/` in the change's project folder. For example: `EVALS_DATA=<project folder>/evals-data <tool folder>/bin/evals check ...`. Without it, a check on a file outside the tool's folder stops before any call and says how to name one; `data/` in the tool's folder is for made-up agents only.

The tool keeps no settings of its own. The first time, ask the person once where they want their case files and data folder, and offer to note the answer where their sessions will find it next time (their own Claude Code instructions or memory). The spend cap is per check, at a default amount unless they ask for another with `--cap`.

## The steps

1. **`evals doctor`, once.** It is free. If it names a problem, tell them the fix in one line and stop there.

2. **The case file, drafted for them.** One Markdown file per agent, kept where they said (above). Its form:

   ```
   # Cases: <the agent's name>

   ## Rubric
   - one line of what good looks like (5 to 10 lines)

   ## Voice sample
   (only where voice matters: a short sample of the person's writing)

   ## Shared context
   (optional: what goes at the top of every task)

   ## Case 1: <a short title>
   Must pass: yes
   Max words: 300
   Must include: <a word or phrase>
   Must not include: <a word or phrase>

   <the task>
   Include: <a file's path, from the case file's folder>
   ```

   - 5 to 15 cases. Each adds to every check, so draft no more than the change needs. Draft them from what the agent is for and what the change touches (with `evals rules` and `evals aim` below where they fit), read and fix every one yourself, and run `evals cases` until it says ok. **Then send them one short message:** one line a case, the rubric, and which cases are marked must pass. **Only the must-pass marks wait for their OK** (a lost must-pass case decides a verdict); they may change anything else, but nothing else waits for them.
   - **Rules already written down save them the drafting.** When the agent has rules a person gave it (feedback notes, lines of its instructions, one a line or a bullet, at most 10), `evals rules <rules file> [<agent file>]` drafts a case for each that tempts the old mistake, with free rule lines where code can check the rule and a rubric line where it can't. One call, with no tools, on their plan; it says its top cost first (`--estimate` makes no call). It writes a new draft beside the rules file (outside the tool's folder for a real agent's rules), never runs a check and never changes either file. Read every drafted case yourself, fix what is off, and treat the result as the drafts above: one short message to them, their OK only on must-pass marks.
   - The rule lines are optional. Mark "must pass" only for what must never break. A rule needs room: the good version should meet it every time, so a word or two never decides a case.
   - **No rubric line weighted "heavily" or "most important"** unless a slip on that line alone should decide a case. In the sanity run, one line said to count heavily let one small slip decide cases, and a version lost to itself.
   - **The answering agent sees only the task, and has no tools.** It never sees the rule lines, the rubric or any file. So each task holds everything the answer needs, and says any limit it must keep, such as how many words.
   - **Save the person's time with `Include:` and the shared context.** A line `Include: <path>` brings in a real post or note as it is, its front matter left out: don't paste and clean it by hand. The path is written from the case file's folder, and the file is at most 50 KB. Only "Include:" and one path counts (a path with spaces in quote marks); any other line starting "Include:" is plain text, and a space before it keeps even a one-word line as text. What the agent would know about the person in real use (an "About me": equipment, goals, what its memory holds) goes once under `## Shared context`, which tops every task; it can be an `Include:` of a note of theirs.
   - **The judges see only the task, the rubric and the voice sample,** never the agent file. A fact both versions' agent files give, that answers will use (a name, a place, a product), reads to them as made up. Put each such fact into the rubric or the task.
   - Run `evals cases <case file> <current file> [<candidate file>]` until it says ok, and act on its notes. A word limit as tight as the length the task asks for: give the rule about 30% more. Each listed number, date or name an answer is likely to use: put it into the rubric or the shared context. A note that the agent's file names tools: make sure each task holds what a tool would fetch.
   - `evals cases` also notes, free and by word counts alone, so each is a reminder to weigh, not an error: a rubric line that applies only in some situation ("when", "if") that no case seems to set up (fix a case or drop the line); two cases that are near copies (make one test something else); a must-pass case with no rule line (add one if code can check what it guards; if not, the judges decide it); a task that asks for no length (say how long; a case with a `Max words` line is not noted, since a case that tests a length rule must not say it).

3. **The candidate, made with them.** Either a changed copy of the file (the current file stays as it is), or the same agent on another model or effort, with no candidate file. Tell them in one or two lines what differs, and get their OK.
   - **Make the copy outside any folder Claude Code loads:** beside the case file, in the change's project folder. Never in the current agent's folder, a skills folder or a project's `.claude/`: Claude Code loads every file there, so the candidate would be live before they say OK (it never replaces anything without their OK). A changed instruction file is never named or placed as one Claude Code loads: give it a name such as `claude-md-candidate.md`, never CLAUDE.md.
   - Model names in full only, such as `claude-sonnet-5-5`. Short names like "sonnet" are refused.
   - An instruction file, such as a CLAUDE.md: `--instructions`, and its model with `--model`.
   - Against plain Claude Code, with nothing added ("does my agent beat plain Claude?"): `--current plain` and only the agent's file after the case file. Plain Claude Code runs on the agent's model and effort unless `--model` or `--effort` says otherwise; those move only the plain side, and the agent keeps its file's model and effort unless `--candidate-model` or `--candidate-effort` is given.
   - A skill can't be checked as a skill yet. Its text can be checked as an instruction file, which tests what it says, not whether it is picked up when it should be. Tell them that before they choose it.
   - Answers have no tools, so an agent whose work is reading files, searching or running commands can't be checked on that part.
   - **Cases aimed at the change.** When the candidate is a changed file and no case puts a changed line to work, run `evals aim <case file> <current file> <candidate file>` before the check (one call, with a small cost limit; `--try` adds a first look that drops drafts the versions tie on, with `EVALS_DATA` as for a check). It writes two or three draft cases beside the case file. Read them, move the good ones in without their `## Why` part, and run `evals cases`. Without such cases, a check of a small change comes out a near tie.

4. **The cost, before spending.** Run the check with `--estimate` first. It calls no model. Pass on the likely cost, the top cost and the cap in one short message ("likely about <likely cost>; top cost <top cost>; the cap stops it at <cap>"), and run it on a yes.
   - On a plan with a 5-hour and a weekly allowance, the check also says, by default, how much of each is used and how much of each it likely takes (`--estimate` too): pass that on in the same short way. It reads this from Claude Code's `/usage`, which is free and calls no model. If the read fails, the check says so in one line and goes on. `--no-usage` (or `EVALS_USAGE=0`) turns it off. How much a check likely takes is learned in each data folder on its own, so a new `evals-data/` can't say it until a check there has measured it.
   - Three answers a case is the default. **`--answers 1` is a cheap first look:** a third of the answer and judge calls, but not a verdict to act on (its page and last line say so). Offer it only when they want a quick, cheap read, and estimate again if they take it.
   - If the top cost is over the cap (the default unless `--cap` says otherwise), nothing starts. Offer fewer cases (`--only`) or a higher cap, as their choice.
   - If the check says both versions give the answers the same instructions, its verdict is chance. Say so before spending anything.
   - Each answer has 5 minutes. For long pieces (about 1,000 words or more) on Sonnet, pass `--answer-minutes 10`: a Sonnet answer at effort high took 236 of the 300 seconds, and two time-outs end a check "incomplete". It raises the top cost. (The owner's call: keep the 5-minute default.)

5. **The check, in the background.** For example:

   ```
   EVALS_DATA=<project folder>/evals-data <tool folder>/bin/evals check --candidate-model claude-sonnet-5-5 <case file> <agent file>
   EVALS_DATA=<project folder>/evals-data <tool folder>/bin/evals check <case file> <current file> <candidate file>
   EVALS_DATA=<project folder>/evals-data <tool folder>/bin/evals check --current plain <case file> <agent file>
   ```

   Run it as a background command: calls go one at a time. 8 cases of short answers take about 30 minutes (about 10 with `--answers 1`); 8 cases of pieces of 600 to 1,200 words take about 1.5 to 3 hours (30 to 60 minutes with `--answers 1`). It prints a line as each answer and case is done. Don't stop it part way unless they ask.

   Several agreed checks, one after another: write them in a queue file, one check a line as typed after `evals check`, and run `<tool folder>/bin/evals queue <queue file>` (with `EVALS_DATA` as above). Run `evals queue --estimate <queue file>` first: it says each check's likely and top cost and their sum, and makes no call. The queue stops at the first check that doesn't end with a verdict and prints the one command to go on.

6. **The page, as it is.** The check's last lines are what it spent, the verdict in one sentence, where `page.md` is, and, on a one-answer check with a verdict, a line saying it is a first look, not a verdict to act on. Read them the verdict sentence. Then give them `page.md` and `answers.md` (in the same folder) as they are: not summed up, not reworded, nothing left out, the way they usually get a file to read.
   - **When this check's pairs are for their ranking** (below), hold both files back: make the sheet first, and give them `page.md` and `answers.md` only after `evals agree` has read their ranking. Until then, read them nothing of the verdict either. The page names each judge's pick and which answer was the candidate's, and `answers.md` labels every answer, so either would tell them what the judges chose.
   - "Stopped" or "Incomplete" is no verdict. Say so plainly: it never means "no worse". To go on from where it stopped, run the same command again (with a higher `--cap` if it stopped at the cap): it keeps every answer and judgment made so far. What it spent before it stopped counts toward the cap, so a check stopped at its cap needs a higher one; its page shows the whole check's spend.
   - **The plan's usage limit.** If Claude Code says the limit is reached, the check (or queue) stops at once with exit code 75, keeps what it made, and says when the limit resets. Pass on that time and the one command it printed to go on; run it then, not before. Don't retry in a loop. A spend limit (usage credits, an organization's budget) stops it the same way and is said as one: it goes on once the limit is raised or resets.
   - **A one-answer verdict (`--answers 1`) is a first look:** before they act on it, offer to run it again with three answers a case, the default (estimate first). With one answer a case, one slip can decide a case: in the sanity run a version lost to itself that way (`sanity-run.md`).
   - Answer their questions about the page. Don't recommend a replacement. The decision is theirs.

## Checking the judges with their own ranking

When they want to know if the judges can be trusted, or before the first replacement:

1. `evals sheet <check folder> [<check folder>...]` makes a blind sheet of 5 pairs, short enough to answer on a phone: 3 close (the panel tied, or its judges split) and 2 clear (every judge made the panel's call), shuffled together. `--pairs N` asks for another number, about three fifths close. It is free, prints the sheet's path, and when a check has too few of one kind, fills from the other and says how many of each it found. A later sheet from the same check leaves out pairs an earlier sheet used, and says when none are left. Only from checks whose `page.md` and `answers.md` they haven't seen, so they rank blind, as the judges do. Don't tell them which pairs are close or clear before they rank.
2. Give them the sheet as it is. They type 1, 2 or tie on the line under each "Your pick", in any editor or on a phone, and may skip any pair. Don't show them the `key.json` beside it, or the judges' votes, before they are done.
3. `evals agree <sheet file>` reads it back, free. Give them what it prints, as it is. Its first line after the count is the panel's agreement with them on the clear pairs, the one that matters most: a "Red flag" there means the panel was sure and they picked the other output. Lead with it, plainly: if their pick stands after a second look, the panel's verdicts on checks like that one can't be trusted yet. On close pairs each judge's rate is information only, and runs low by design: it names no judge as weak from them, and neither do you. If they may rank more pairs from that check, make that next sheet first and hold both files back until they are done ranking it: a later sheet from a check whose page they have read is not blind. Then give them the check's `page.md` and `answers.md`, as step 6 says.
````

Previous: [Rex](https://oneillo.com/ai-setup/skills/rex/).

[Back to Skills](https://oneillo.com/ai-setup/skills/)

[Borrowed something? Tell me how it went](https://www.linkedin.com/in/oneillo/)

Text: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Scripts: [MIT](https://opensource.org/license/mit).
