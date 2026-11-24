# Tune setup

- Kind: Claude Code skill
- Address: https://oneillo.com/ai-setup/skills/tune-setup/
- Last updated: 2026-11-22

> AI-written and cleaned from one person's working setup. Placeholders in angle brackets are not real values: ask your user for theirs. The newest version is at https://oneillo.com/ai-setup/skills/tune-setup/.

Researches current Anthropic guidance, updates a guidance file, and proposes specific improvements to a Claude Code setup.

- **What it is:** Tune setup is a Claude Code skill that checks a Claude Code setup against current Anthropic guidance and proposes ranked improvements without applying them.
- **How it starts:** You type `/tune-setup`. Claude doesn't start it by itself.
- **Where the file goes:** `~/.claude/skills/tune-setup/SKILL.md`, its other files beside it
- **Needs:** Nothing extra
- **To make it yours:** Put your own values where you see `<project-folder>`.
  Taken out for privacy: 1 project folder and 1 path.

**Works with:** [Process feedback](https://oneillo.com/ai-setup/skills/process-feedback/) and [Save session](https://oneillo.com/ai-setup/skills/save-session/).

## The skill file (`SKILL.md`)

The instructions for a skill that researches current Anthropic guidance, updates one knowledge file, reviews the setup against it, and writes proposals for the owner to approve.

```
---
name: tune-setup
description: Research current Anthropic guidance on prompting and Claude Code, update the LLM guidance knowledge file, and propose specific improvements to agents, skills, and CLAUDE.md
disable-model-invocation: true
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, WebSearch, WebFetch, Agent
---

# Tune Setup

Keep Orlando's Claude Code setup current with Anthropic's guidance. This skill replaces a dedicated "LLM Expert" agent: its knowledge lives in one file that only this skill reads, so other agents never pay for it in context.

Run it when a new Claude model or major Claude Code release ships, or when Orlando asks.

## 1. Load what we already know

Read `~/.claude/skills/tune-setup/llm-guidance.md`. If it doesn't exist, create it with the structure in step 3. Note the last-run date, which models it covered and the folder that holds that run's lookup; read that lookup and research only what's new since then.

For a full review, use `~/ai/projects/ai-setup/<project-folder>/` (in `ai-setup/zzCompleted/` once closed) as the pattern: parallel readers by area, a checker that tries to disprove each finding, and one page for Orlando.

## 2. Research

Use primary sources: Anthropic's docs (prompt engineering, model migration guides, Claude Code docs on memory and CLAUDE.md, skills, subagents, hooks, settings), Anthropic's engineering blog and news, and model release notes. Use secondary sources only to find primary ones.

- For broad runs, split research into parallel subagents by area (prompting, Claude Code features, model changes).
- Write what you find to `guidance.md` in this run's project folder (step 5), with the source URL and date for every finding.
- If a claim can't be traced to a primary source, mark it unverified. Never present a guess as guidance.

## 3. Update the knowledge file

`llm-guidance.md` keeps only what can't be looked up or measured again. Sections:

- **Last run:** date, current models, Claude Code version, and the path of this run's `guidance.md`.
- **Lessons from this setup:** what worked or didn't here, from feedback memories (see `/process-feedback`) and past audits in `~/ai/projects/ai-setup/`, plus setup decisions. Each decision gets its reason and a "revisit when" condition.

## 4. Check the setup against it

Review `~/.claude/CLAUDE.md`, `~/ai/CLAUDE.md`, `~/.claude/agents/`, `~/.claude/skills/`, `~/.claude/settings.json` (hooks included), the status line script, `~/ai/.claude/`, the vault's rules file, memory indexes and the memory files, agent memory (`~/.claude/agent-memory/`), and the tools in `~/ai/tools/`. Measure the size of everything that loads every session (CLAUDE.md files, memory indexes, model-invocable skill descriptions), since that's the recurring token cost.

Also:
- Challenge past decisions. For each entry in Lessons whose "revisit when" condition has been met, or that new guidance undercuts, propose keeping, changing or dropping it. Removing something that no longer earns its place counts as much as adding something.
- Re-check effort and model settings against https://code.claude.com/docs/en/model-config. Settings can silently stop applying when a new model ships.
- Review memory as `~/.claude/skills/process-feedback/SKILL.md` describes, and put what it would change with the other proposals.
- If Orlando is at the Mac, ask him to run `/doctor prompt-audit`, `/skill-doctor`, `/context all`, and `/insights` and paste the output. They audit prompts for outdated patterns, show unused skills, list built-in features he isn't using, and measure the context this skill can't (system prompt, MCP tools, bundled skills).

## 5. Propose, don't apply

Write `~/ai/projects/ai-setup/<yyyymmdd>-tune-setup/proposals.md`: a short summary, then proposals ranked by value. For each: what to change (file and passage), why (with source), expected effect, and risk. Also write a `context.md` there (format in `~/.claude/skills/save-session/SKILL.md`). Make no changes until Orlando approves specific proposals. When every proposal is applied or decided, move the folder to `~/ai/projects/ai-setup/zzCompleted/`.

## Rules

- Plain language, no em dashes.
- Never put this material in `~/ai/CLAUDE.md` or workspace memory; every agent loads those.
- Say plainly when guidance conflicts with how Orlando likes to work, and let Orlando decide.
```
