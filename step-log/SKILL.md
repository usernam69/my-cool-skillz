---
name: step-log
description: Use when the user types /step-log or asks to see what is happening step by step, why a choice was made, or a running log of the work
---

# Thinking Process

## Overview
Show the work as it happens. Claude's private reasoning can't be displayed, so this skill gives a visible running log of each step: what is being checked, why, and what was decided. The log is written for the user, not a transcript of hidden thoughts.

## Steps
1. **Announce the goal.** One line: what the task is and what "done" looks like.
2. **Log each step** as a numbered line before doing it, in this form:
   `Step N: <what I'm doing> - Why: <reason> - Result: <what I found or decided>`
   Fill in Result after the step. Keep each line to one sentence.
3. **Log every decision point.** When there's a choice, write the options considered and which one was picked, and why.
4. **Log surprises.** If something fails or doesn't match what was expected, say so in the log and state the new plan.
5. **End with a summary.** Finish with the outcome and a list of the steps taken.

## Output shape
```
**Goal:** <one line>

Step 1: Read the skill folder - Why: find the current instructions - Result: SKILL.md has 3 steps.
Step 2: ...
Decision: option A vs B - picked A because <reason>.

**Outcome:** <result>
```

## Common Mistakes
- Claiming to show hidden reasoning. Log actions and reasons, not private thoughts.
- Writing the log after the work instead of during it.
- Long paragraphs per step. Keep each line short.

