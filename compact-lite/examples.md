# compact-lite test cases

## 1. Pasted log
User pasted a 2,000-line build log, then asked to fix the first error.
Good: keeps the user's question word for word, the log as first/last 5 lines plus "(2,000-line build log from npm run build)", the error text that's still unsolved, and "Next step: fix <error> in <file:line>".
Bad: the whole log, or no mention that a log was pasted.

## 2. Long build session
40 files changed across 3 features, 2 done, 1 half done.
Good: one line per finished feature, the half-done one with files and the exact next step, under 1,500 words.
Bad: code snippets of every file.

## 3. Standing rule
User said "always push and tag after every change".
Good: the rule word for word under preferences.
Bad: the rule summarised as "user likes releases".
