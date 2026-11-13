---
name: bugger
description: Use when the user types /bugger or asks to find and fix bugs in their code while being told about each one along the way
---

# Bugger

## Overview
Hunt for real bugs in the user's code, fix each one, and tell the user about every bug as it is found and fixed, not only at the end.

## Steps
1. **Pick the target.** If the user named files, a feature, or an error, use that. Otherwise use the current uncommitted changes (`git diff`), then the most recently changed files. Say in one line what you're checking.
2. **Hunt.** Read the code and look for real defects: crashes, wrong results, unhandled errors, edge cases (empty, null, zero, huge input), off-by-one, race conditions, resource leaks, security holes. Ignore style and naming.
3. **For each bug, report it before fixing:**
   `Bug N [crash|wrong result|data loss|security|cosmetic]: <file:line> - <what's wrong> - <what goes wrong for the user>`
   Only report bugs you can explain with a concrete failing case. If unsure, label it "Possible" and don't fix it without asking.
   **Second opinion:** before reporting a "Possible" bug, argue the other side in one sentence (why it might not be a bug). Keep it only if the failing case survives.
4. **Fix it** with the smallest change that solves it, matching the surrounding code. Then post:
   `Fixed N: <what changed>`
   Use superpowers:systematic-debugging for anything that needs investigation.
5. **Verify.** Run the project's tests, build, or type check if one exists. If something now fails, say so and fix or revert.
6. **Log it.** If the project has a `BUGS.md`, add a dated section listing each fixed bug (severity, file, one line). Create one only if the user asks.
7. **Summarize.** List bugs fixed, possible bugs left for the user to decide, and what was verified. If no bugs were found, say so plainly.

## Common Mistakes
- Saving all bug reports until the end instead of telling the user as you go.
- "Fixing" style or refactoring code that wasn't broken.
- Reporting guesses as confirmed bugs.
- Claiming fixes work without running tests or a build.
- Committing changes. Leave committing to the user unless they ask.
