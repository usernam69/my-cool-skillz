---
name: mega-idea-probe
description: Use when the user types /mega-idea-probe or asks for a huge number of ideas (50+) about what is being discussed in the current conversation
---

# Mega Idea Probe

## Overview
Like idea-probe, but huge: come up with **at least 50 ideas** drawn from the **current conversation** (not the repo or project at large), then let the user decide whether to build the best one. Only propose genuinely high-impact, high-value ideas — skip trivial, cosmetic, or low-effort tweaks. Every idea must be worth the user's time.

## Steps
1. **Read the chat.** Identify what the user is working on, talking about, or stuck on in this conversation. If the chat is empty or has no clear topic, ask what to brainstorm about and stop.
2. **Generate 50 or more ideas.** Count them — never stop below 50. Each idea is one line: a short bold name + one short sentence. Group them under a few headings (e.g. Quick wins, Bigger features, Wild ideas) to keep the list scannable. Push past the obvious: after the first 20, go for unusual angles, combinations, and opposites. No duplicates or reworded variants.
3. **Pick the top 3.** Mark the single best with ⭐ and give one sentence on why it wins. List two runners-up by number.
4. **Ask.** End with exactly one question: "Want me to build ⭐ <name> now, or keep these as ideas?" Use AskUserQuestion when available, with options "Build best", "Build all" and "Just the ideas".
5. **If they say build best:** build only the starred idea (or the one they name instead), following any relevant process skills. Then report what was built.
   **If they say build all:** build every idea on the list, one at a time, in order. After each one, report its result in one line. Then give a final summary of everything built and anything that failed or was skipped.
6. **If they say just the ideas:** stop. Do nothing else.

## Output shape
```
**Mega ideas from this chat** (N ideas)

### Quick wins
1. **Name**: one sentence.
...
### Bigger features
...
### Wild ideas
...

⭐ **Best: Name**: why it wins.
Runners-up: #12, #37

Want me to build ⭐ Name now, or keep these as ideas?
```

## Common Mistakes
- Stopping below 50 ideas.
- Padding with near-duplicates to hit the count.
- Pulling ideas from the codebase instead of the conversation.
- Starting to build before the user answers, or building more than one idea unless the user chose "Build all".


