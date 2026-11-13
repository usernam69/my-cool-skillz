---
name: loop
description: Use when the user types /loop or asks to loop/repeat the message they just sent.
---

# Loop

## Overview
Repeats the message the user sent a specified number of times.

## Steps
1. **Get the inputs.** From the user's message, find the message text and how many times to repeat it. If either is missing or unclear, ask (use AskUserQuestion when available). Ask only what you need, in one round. Default count is 3 if not specified.
2. **Validate.** Ensure the count is a positive integer (1–100). If out of range, clamp or ask.
3. **Output.** Print the message repeated the requested number of times, each on its own line (or as a single block if the user prefers).
4. **Report.** Confirm how many times it was looped.

## Common Mistakes
- Forgetting to ask for the count when the user only gives a message.
- Looping more than 100 times and flooding output.
- Mixing the message and count in unclear phrasing (e.g., "loop hello 5" vs "loop 5 hello").
