---
title: "I've Been AI-Pilled: My Journey From Chatbots to Custom Agents"
date: 2026-03-13
draft: false
description: "How I went from occasionally using chatbots to managing a team of five custom AI agents — and why the benefits are compounding."
tags: ["ai", "llm", "productivity", "agents"]
cover:
  image: ""
  alt: ""
  hidden: true
---

I was slow to start using generative AI, but over the last 7 months, AI has fundamentally changed how I work. I've gone from occasionally using AI to write text, to using it to create Python scripts, to now having a team of five custom AI agents that I collaborate with daily. I'm seeing how quickly the benefits are compounding, and as a result, I've been AI-pilled.

I began learning about LLM-based gen AI in earnest in 2024. I read all the most popular books at the time, but my exposure remained primarily theoretical. I learned how LLMs work fundamentally, but the biggest practical takeaway was the idea of assigning a persona to chatbots to improve their output. That's basic prompt engineering, e.g. "You are a copywriter with 15+ years of experience in consumer tech. Help me write a marketing email about this product." On the rare occasion I used a chatbot, I always remembered to assign it a persona.

Last August, I joined a project at work that was the turning point for my AI enthusiasm. In that project, I had to manually build a large JSON file that would require a lot of ongoing updates. On a whim, I decided to see if I could use a chatbot to write a Python script to go from JSON to Excel and vice versa. That would allow me to make updates in Excel, which would be faster, and then generate the JSON programmatically, reducing the risk of errors. Within 30 minutes, I had a working prototype that ultimately saved me countless hours over the coming months.

I'm OK at Python, but I realized LLMs are much better. So, I began to write a lot of Python scripts this way to automate repetitive or time-consuming tasks, like resizing images or creating Word docs from copy I had in Excel files, in order to stay on top of the workload for the project.

I was soon using chatbots weekly for other things. I began paying attention to what model the chatbot was using, switching to the latest frontier models whenever possible. That had a noticeable impact on the quality of the copy and ideas that I was getting from the chatbots. Especially when I paired better models with a well-crafted persona and a collaborative approach.

I got tired of typing different versions of the same persona prompts whenever I started a new chat. Often I was too busy and moving too quickly to write something better than "You're a [blank] with X years of experience." It happened enough times, that I realized I could save some time without sacrificing quality by creating reusable persona prompts for different types of tasks. At first I wrote them myself, and they were ok, but not great. By asking the chatbot to help me craft the persona, I was able to take them to the next level. I kept the prompts in Word docs so I could copy and paste them into the start of my chat sessions depending on what I was working on. To save a little more time, I'd pin the chat in the sidebar and rename it to something like "Copywriter" or "Data Wizard," so I could quickly return to the right chat based on what I was working on.

I'd work with the same chat for up to a week because I wasn't aware of context rot (where long, ongoing conversations with LLMs start to produce worse results). That's ok though, because it led to another breakthrough for me. I started to ask the chatbots at the end of each week how we could improve the persona prompt I'd initially started the conversation with based on our interactions. The chatbot would suggest some ideas, and after a few revisions and back-and-forths, it would write an updated version that I would use in the next week's chat. That created a feedback loop to improve my personas on an ongoing basis.

For example, I learned that LLMs guesstimate how long copy is after noticing that their character counts for marketing copy were often wrong. That's not great when you're writing ad copy that has specific character constraints. I updated my Copywriter persona prompt with instructions to count each individual character when writing against copy constraints. After that, I no longer had to worry about getting copy options that were too long for the character constraints I'd provided. It was like giving an employee feedback, except the chatbot immediately incorporated that feedback into how it worked.

That's the core idea in the framework that is now guiding my AI usage: that I'm the manager of a team of AI agents. They're incredibly smart, but also kind of dumb. They have a lot of expertise, but they're also clueless about the specifics of where I work and what I'm working on.

The more effort I put into developing my team and providing what they need, the better the quality of work I get from them. And the benefits are compounding over time. I spend less time correcting simple issues and more time refining and improving what we're working on. I spend less time providing the same context over and over again to my agents. Instead, they have a growing knowledge base to inform their work. I spend less time tweaking the documents they create because they have actual examples of the various documents I have to write to refer to. I'm capturing feedback and improving every aspect of my setup daily, which makes it even better the next day and miles ahead of using a run-of-the-mill chatbot.

I'm going to go into more detail about this framework in coming posts and explain how I've implemented it in [Kiro CLI](https://kiro.dev/cli/), an AI coding tool I use at work primarily for non-coding tasks, and how I'm now implementing it in [Claude Code](https://code.claude.com/docs/en/overview) at home.
