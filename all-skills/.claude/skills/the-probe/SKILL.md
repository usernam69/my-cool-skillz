---
name: the-probe
description: Use when the user types /the-probe or asks for an enormous number of ideas (1000+) about what is being discussed in the current conversation
---

# The Probe

## Overview
The biggest idea probe: come up with **at least 1000 ideas** drawn from the **current conversation**, saved to a file, then let the user decide what to build. Only for very big projects. Only propose genuinely high-impact, high-value ideas — skip trivial, cosmetic, or low-effort tweaks. Every idea must be worth the user's time.

## Steps
1. **Confirm first. Do nothing else until answered.** Ask exactly:
   "Are you sure you want to do this? This will take a while. USE THIS FOR ONLY VERY BIG PROJECTS"
   Use AskUserQuestion when available, with options "Yes, run The Probe" and "No, use /mega-idea-probe instead". If they say no, stop (or run mega-idea-probe if they picked that).
2. **Read the chat.** Identify what the user is working on. If there's no clear topic, ask what to brainstorm about and stop.
3. **Plan categories.** Split the topic into 20+ categories (e.g. quick wins, features, design, performance, marketing, wild ideas, opposites, mashups). Aim for about 50 ideas per category.
4. **Write ideas to a file, in batches.** 1000 ideas don't fit in one reply. Write them to `the-probe-<topic>-<YYYY-MM-DD>.md` in the current folder, one category at a time, appending each batch. Each idea is one line: number, short bold name, one short sentence. After each batch, post one progress line in chat: `Category N/M done - X ideas so far`.
5. **Count.** Before finishing, count the numbered lines in the file. If under 1000, add more categories until it reaches 1000. No duplicates or reworded variants.
6. **Pick the top 10.** In chat, list the 10 best ideas by number with one line each, and mark the single best with ⭐.
7. **Ask.** End with: "Want me to build ⭐ <name> now, or keep these as ideas?" with options "Build best", "Build top 10" and "Just the ideas". Build only what they choose, then report what was built.

## Common Mistakes
- Starting before the user confirms.
- Trying to put all 1000 ideas in one chat message instead of a file.
- Stopping below 1000, or padding with near-duplicates to hit the count.
- Pulling ideas from the codebase instead of the conversation.
- Building anything before the user answers.
