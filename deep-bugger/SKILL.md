---
name: deep-bugger
description: Use when the user types /deep-bugger or wants every bug found and fixed, not just the obvious ones, with repeated passes until no more turn up
---

# Deep Bugger

## Overview
A relentless version of bugger: hunt, fix, and hunt again in repeated passes. Every bug found is a sign there are more nearby. Stop only when a full pass finds zero new bugs.

## Steps
1. **Isolate first.** Never run the code or its installs on the user's real system. Pick the sandbox that fits:
   - **Python code:** create a venv in a scratch folder (e.g. python -m venv <scratch>\venv) and install dependencies only into it. Run everything with that venv's interpreter.
   - **Node or web code:** copy the target into a scratch folder and run npm install there, not in the original folder.
   - **Whole apps or anything that needs full isolation:** use Docker or Windows Sandbox if the user has them available. Ask first if neither is set up.
   Work on a copy when fixing, so the original stays untouched until the user approves the fixes. Say which sandbox you chose in one line.
2. **Pick the target.** If the user named files, a feature, or an error, use that. Otherwise use the uncommitted changes (`git diff`), then the most recently changed files. Say in one line what you're checking and list the files.
3. **Run passes.** Each pass uses a different lens, so a later pass catches what an earlier one missed:
   - Pass 1: crashes and unhandled errors
   - Pass 2: wrong results and logic (off-by-one, wrong conditions, wrong defaults)
   - Pass 3: edge cases (empty, null, zero, negative, huge input, unicode, missing files)
   - Pass 4: state and timing (race conditions, stale state, ordering, async mistakes)
   - Pass 5: resources and security (leaks, unclosed handles, injection, secrets, unsafe paths)
   - Pass 6: integration (callers and callees of every function you changed, config, platform differences)
   Announce each pass in one line: `Pass N: <lens>`.
4. **Report each bug before fixing:**
   `Bug N [crash|wrong result|data loss|security|cosmetic]: <file:line> - <what's wrong> - <what goes wrong for the user>`
   Only report bugs with a concrete failing case. Unsure? Label it "Possible", argue the other side in one sentence, and don't fix it without asking.
5. **Fix it** with the smallest change that solves it, then post `Fixed N: <what changed>`. Use superpowers:systematic-debugging for anything that needs investigation.
6. **Found a bug? Find more.** After each fix, look for the same mistake elsewhere: grep for the same pattern, check sibling functions, and re-check the code your fix touched.
7. **Verify after every pass.** Run the project's tests, build, or type check if one exists. If something fails, fix or revert it before moving on.
8. **Keep going.** After pass 6, if any pass found a bug, run all six lenses again over the changed areas. Stop only when a full round finds zero new bugs. Post `Round N: X new bugs` after each round.
9. **Log it.** If the project has a `BUGS.md`, add a dated section listing each fixed bug. Create one only if the user asks.
10. **Summarize.** Total bugs fixed by severity, possible bugs left for the user to decide, rounds run, and what was verified. Then say honestly what wasn't checked (files skipped, untestable areas). Never claim the code is bug-free; say "no more found after N rounds."

## Common Mistakes
- Stopping after the first round because it found some bugs.
- Fixing one instance of a bug without searching for the same mistake elsewhere.
- Reporting guesses as confirmed bugs to make the count bigger.
- "Fixing" style or refactoring code that wasn't broken.
- Claiming fixes work without running tests or a build.
- Committing changes. Leave committing to the user unless they ask.


