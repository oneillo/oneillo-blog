---
title: "When Your AI Agent Can't Do the Job"
date: 2026-06-14
draft: false
description: "When your agent can't do what you need, you've hit a capability gap. There are four ways to fix it, depending on how technical you want to get."
tags: ["ai", "agents", "productivity"]
cover:
  image: ""
  alt: ""
  hidden: true
---

# The Third Agentic AI Gap

When I first started building my mental model for agentic AI, I identified two types of gaps that you need to fix when working with agents: 

1. **[Knowledge gaps](https://oneillo.com/posts/managed-ai-framework-knowledge-base/)**: agent doesn't know something you want it to know, like your name or what type of equipment you have in your home gym.
2. **[Behavioral gaps](https://oneillo.com/posts/managed-ai-framework-team-rules-and-skills/)**: agent doesn't behave the way you'd like or expect. For example, it guesstimates character counts instead of counting characters.

I've since realized there's a third type: **capability gaps**. 

Capability gaps are when the agent isn't able to do something you want it to do. For example, you might want your agent to help you manage your email, but it's not able to connect to your email program. Or you might want it to help you edit videos, but it isn't able to work with video directly. 

I've noticed this is the first gap people at work run into when I help them set up an agentic AI tool, like Kiro or Claude Code. They normally have a use case in mind that they want to use agents to help them with, and they quickly run into the agent not being able to do that specific thing (email, calendar, and Slack have been the most common).

There are multiple options to address capability gaps, in increasing order of difficulty: 

* **Built-in tools:** agentic AI apps, like Amazon Quick desktop app or Claude desktop app, that target a broader non-technical audience have built-in options for connecting and enabling additional functionality. They might be called plugins, connectors, or something similar. This is the first thing you should check for, as they will cover the most universal use cases, like email and calendar.
* **Command-line tools:** agents can use command-line tools to get things done with your computer, like [ffmpeg](https://ffmpeg.org) to work with video, [pandoc](https://pandoc.org) to convert documents, or [Python](https://www.python.org) for doing just about anything. This option often means looking for open source packages that you can install on your computer (or asking your agent to do it for you). 
* **Model Context Protocol servers (MCPs):** MCPs are lightweight programs that allow your agent to connect to and use other apps and services, like Figma or email. Unlike command-line tools, where you just install and the agent can use it, MCPs often require you to install and then configure your agentic AI harness to enable them. One thing to watch: every MCP loads a set of tools into your agent's context window, which eats into its capacity to do work. Only enable MCPs you're actually using.
* **Do-it-yourself:** Lastly, you can use the agents to create a custom solution to enable what you need. The ability to do this depends on a variety of factors, like what specifically you need and how the app/service is built. For example, I use OmniFocus at work to manage my to do list, and it has become the place where all of the todos my agents triage for me are created. In order to do this, I first worked with my agent to create an MCP so any agent could use OmniFocus on my computer.

If you run into a capability gap and aren't sure how to proceed, start by asking your agent. It can often figure it out or find a solution on its own.

If that doesn't work, search online, in Slack, or in available wikis. Someone may have already solved it, and if so, you can paste the solution directly into your session and have your agent take it from there.
