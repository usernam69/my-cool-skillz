# my-cool-skillz

A collection of personal [Claude Code skills](https://docs.claude.com/en/docs/claude-code/skills) plus a snapshot of every skill found on this machine.

## Personal skills

| Skill | What it does |
|---|---|
| bugger | Hunt and fix bugs, reported one at a time |
| compact-lite | Compact a long session keeping what matters |
| deep-bugger | Repeated bug-hunting passes until none remain |
| drl | Build a game for AI models to compete in, then train with DRL |
| fullcompact | Produce a /compact command keeping ~100% of context |
| idea-probe | Batch of ideas about the current conversation |
| loop | Repeat a message N times |
| makeitbetter | Make one bold change to improve an app/project |
| mega-idea-probe | 50+ ideas about the current conversation |
| new-skill | Scaffold a new personal skill and sync/zip it |
| smarter | The most wrong answer possible (joke) |
| step-log | Step-by-step log of work and choices |
| the-probe | 1000+ ideas about the current conversation |

## All skills on this machine

- `skills_sorted.txt` — 381 unique skill names, sorted
- `skills_with_folders.txt` — 605 full paths, sorted
- `all-skills/` — copy of every `SKILL.md` found, preserving original paths
- `all-skills-part1.zip` … `all-skills-part5.zip` — zipped chunks (≤2.5 MB each)

## Usage

Drop any skill folder into `~/.claude/skills/<name>/` then run:

```
powershell -ExecutionPolicy Bypass -File "C:\Users\Dovi\.claude\skills\sync-skills.ps1"
```

This syncs to every Claude Code profile and zips for Claude chat upload.