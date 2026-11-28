# How the setup improves

- Kind: Claude Code walk-through
- Address: https://oneillo.com/ai-setup/guides/how-the-setup-improves/
- Last updated: 2026-11-28

> AI-written and cleaned from one person's working setup. Placeholders in angle brackets are not real values: ask your user for theirs. The newest version is at https://oneillo.com/ai-setup/guides/how-the-setup-improves/.

Shows how corrections become lessons, reviewed and tested changes, and an approved update, so the setup improves without getting worse.

This setup changes in a loop. Each step hands something to the next, and nothing replaces what runs now until Orlando says so.

## Corrections become lessons

It starts in an ordinary session. When Orlando corrects Claude, auto memory saves the correction as a `feedback` memory. It takes effect in the next session on its own. Over time these pile up, and every memory index line loads in every session, so they need regular review.

## The Process feedback skill reviews them

The Process feedback skill (`/process-feedback`) reads every saved memory, for the workspace and for each agent. It suggests one action for each: keep it as a memory, promote it to a rules file, an agent definition, or a skill, turn it into a hook, merge it with another, or delete it. It leans toward the narrowest place that covers the lesson, and toward merging and deleting. It shows only what it would change, waits for Orlando's input, and then applies what was agreed. The Tune setup skill runs it too.

## The Tune setup skill proposes changes

The Tune setup skill (`/tune-setup`) researches current Anthropic guidance and checks the rules, skills, agents, and settings against it. Its proposed memory changes join its other findings. It writes one proposals page, ranked by value, with what to change, why, the expected effect, and the risk. It applies nothing until Orlando approves specific proposals. It also says plainly when guidance conflicts with how he likes to work, and he decides.

## The Evals skill and tool check the change

Some proposals, such as a changed agent, a new model or effort, or an edited instruction file, can be tested before they go live. The Evals skill runs the Evals tool, which checks that a change is no worse than the current version, using blind judging by three judges. Both versions answer the same cases. The judges compare the answers, and Orlando gets one page with the verdict.

The skill has firm limits. It never proposes a change by itself, because a check starts only after Orlando and a session have decided on a change. It never edits the current file. It also works on a copy kept outside any folder Claude Code loads, so the candidate can't go live early.

## Orlando's OK puts it in place

The verdict is information, not a decision. Whether the candidate replaces the current version is Orlando's call, in his own words. Until then, nothing changes. Once he agrees, the change goes in.

## The pieces it joins

- [Process feedback](https://oneillo.com/ai-setup/skills/process-feedback/)
- [Tune setup](https://oneillo.com/ai-setup/skills/tune-setup/)
- [Evals](https://oneillo.com/ai-setup/skills/evals/)
- [Evals](https://oneillo.com/ai-setup/evals/)

[Back to AI Setup](https://oneillo.com/ai-setup/)

[Borrowed something? Tell me how it went](https://www.linkedin.com/in/oneillo/)

Text: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Scripts: [MIT](https://opensource.org/license/mit).
