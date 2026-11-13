---
name: idea-probe
description: Use when the user types /idea-probe or asks for a batch of ideas about what is being discussed in the current conversation
---

# Idea Probe

## Overview
Come up with many ideas drawn from the **current conversation** (not the repo or project at large), then let the user decide whether to build the best one. Only propose genuinely high-impact, high-value ideas — skip trivial, cosmetic, or low-effort tweaks. Every idea must be worth the user's time.

## Steps
1. **Read the chat.** Identify what the user is working on, talking about, or stuck on in this conversation. If the chat is empty or has no clear topic, ask what to brainstorm about and stop.
2. **Generate 8–12 ideas.** Each idea is one line: a short bold name + one sentence on what it is. Mix safe, practical ideas with a few bold or unusual ones. No duplicates or reworded variants.
3. **Pick the best one.** Mark it with ⭐ and give one sentence on why it beats the rest.
4. **Ask.** End with exactly one question: "Want me to build ⭐ <name> now, or keep these as ideas?" Use AskUserQuestion when available, with options "Build best", "Build all" and "Just the ideas".
5. **If they say build best:** build only the starred idea (or the one they name instead), following any relevant process skills. Then report what was built.
   **If they say build all:** build every idea on the list, one at a time, in order. After each one, report its result in one line. Then give a final summary of everything built and anything that failed or was skipped.
6. **If they say just the ideas:** stop. Do nothing else.

## Output shape
```
**Ideas from this chat**
1. **Name**: one sentence.
...
⭐ **Best: Name**: why it wins.

Want me to build ⭐ Name now, or keep these as ideas?
```

## Common Mistakes
- Pulling ideas from the codebase instead of the conversation.
- Starting to build before the user answers.
- Building more than one idea unless the user chose "Build all".


