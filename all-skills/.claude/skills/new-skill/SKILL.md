---
name: new-skill
description: Use when the user types /new-skill or asks to create a new personal skill or slash command
---

# New Skill

## Overview
Create a new personal skill, install it in every Claude Code profile, and zip it for Claude chat, all in one go.

## Steps
1. **Get the basics.** From the user's message, find the skill's name and what it should do. If either is missing or unclear, ask (use AskUserQuestion when available). Ask only what you need, in one round.
2. **Name it.** Lowercase letters, numbers and hyphens only (e.g. `mega-idea-probe`). If `C:\Users\Dovi\.claude\skills\<name>` already exists, ask before overwriting.
3. **Write `C:\Users\Dovi\.claude\skills\<name>\SKILL.md`** in this shape:
   ```
   ---
   name: <name>
   description: Use when the user types /<name> or <the situations it should trigger on>
   ---

   # <Title>

   ## Overview
   <What it does, 1–2 sentences.>

   ## Steps
   1. ...

   ## Common Mistakes
   - ...
   ```
   The description says only *when* to use it, not how it works. Keep the whole file under ~500 words.
4. **Sync.** Run:
   `powershell -ExecutionPolicy Bypass -File "C:\Users\Dovi\.claude\skills\sync-skills.ps1"`
   Confirm the output shows the new skill synced and zipped.
5. **Report.** Tell the user:
   - What the skill does, in a few bullets.
   - Claude Code: start a new session, then type `/<name>`.
   - Claude chat: upload `C:\Users\Dovi\.claude\skills\zips\<name>.zip` under Settings → Capabilities → Skills.

## Common Mistakes
- Writing straight into a profile folder instead of `C:\Users\Dovi\.claude\skills` (it then never gets zipped or synced).
- Forgetting to run the sync script.
- Putting the workflow in the description instead of the body.
