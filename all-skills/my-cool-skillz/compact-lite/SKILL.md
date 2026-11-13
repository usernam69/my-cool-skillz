---
name: compact-lite
description: Use when the user types /compact-lite or wants to compact a long Claude Code session while keeping what matters, without the summary getting too big to fit
---

# Compact lite

## Overview
Write a ready-to-paste `/compact` command whose instructions keep everything needed to continue the work, but refer to code by file and line instead of copying it. A summary that tries to keep "100%" of a long session can be too big for compaction to work.

## Steps
1. Look at the conversation: the user's requests (in order), what was built or changed, decisions made, open tasks, and the exact next step.
2. Output one code block containing a single line that starts with `/compact` and then these instructions:
   - Keep every user message word for word, except long pasted content (logs, files, transcripts over ~20 lines): keep its first and last few lines and say what it was.
   - Keep the task list: done, in progress, not started, skipped (and why).
   - Keep file paths, function names, commands that worked, versions, URLs, and IDs exactly.
   - For code: name the file, the function and the line, and say what changed in one sentence. Don't paste whole files or long snippets; keep only short snippets (under 10 lines) that are hard to find again.
   - Keep errors that are still unsolved word for word; summarise solved ones in one line with the fix.
   - Keep user preferences and corrections ("don't do X", "always Y").
   - End with "Next step:" and exactly what to do next.
3. **Size check.** Count the words in the instructions. Over ~1,500 words: cut solved errors and old snippets first, then merge finished tasks into one line each. Say the final word count.
4. Tell the user in one line to paste it.

## When to suggest it
If a session has had many large tool outputs or a long build, and the user starts a new big task, suggest `/compact-lite` once in one line. Don't run it yourself.

## Test cases
`examples.md` in this folder has sample sessions and what a good result keeps. Check a changed version of this skill against them.

## Common Mistakes
- Asking for every snippet "in full": the summary can outgrow the space it is meant to free.
- Dropping the user's exact words: they are the source of truth after compaction.
- Forgetting the next step, so the new session has to rediscover it.
