---
name: fullcompact
description: Produces a ready-to-paste /compact command that tells Claude Code to keep as close to 100% of the conversation as possible. Use whenever the user types /fullcompact or asks to compact the whole chat, fully compact, keep everything when compacting, or avoid losing context during compaction in Claude Code.
---

# fullcompact

The built-in /compact command cannot be run by a skill, but it accepts custom instructions that control what the summary keeps. This skill writes those instructions so nothing important is dropped.

## What to do

1. Reply with the command below inside a single code block, with no other commentary except one line telling the user to paste it and press Enter.
2. If the conversation has a specific focus (a feature, bug, or file set), append one short sentence naming it at the end of the instructions, so the summary weights it correctly.

## Command to output

```
/compact Preserve 100% of this conversation. Do not drop, shorten, or merge anything. Keep: every user request and instruction in order, all preferences and corrections the user gave, every decision and the reasoning behind it, all file paths touched with what changed in each, all code written or edited (full snippets, not descriptions), every command run and its result, all errors and how they were fixed, all open tasks and their status, and the exact next step. Quote the user's key wording verbatim. Prefer a long, complete summary over a short one.
```

## Why

Default compaction favors brevity and tends to lose corrections, exact code, and small decisions. Explicit keep-everything instructions push the summary toward completeness, at the cost of freeing less space.
