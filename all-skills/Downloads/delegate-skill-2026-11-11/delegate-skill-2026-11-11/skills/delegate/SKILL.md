---
name: delegate
description: Run as a lead (Fable, Opus or Sonnet) that writes one spec, hands the token-heavy work — writing code, reading files, running, testing and fixing — to cheaper Claude sub-agents (Haiku workers by default, or Sonnet or another model as the whole team whenever the user asks for it), and reviews the result once, so the job costs clearly less than doing it solo at similar quality. Use this skill whenever you are asked to build, implement, fix, refactor, review or test something that takes more than a handful of steps — an app, a game, a website, an animation or film, a feature, a code review, a multi-file change — and whenever the user mentions orchestrating, delegating, saving cost or tokens with sub-agents, Haiku or Sonnet sub-agents, "orchestrate Sonnet", workers or a team of agents, even if they don't name this skill.
---

# Delegate: the lead's tokens buy judgment, the workers' tokens buy volume

The goal is the result a solo run would have produced, at similar quality, for
clearly less money. It is not a way to build more, or to check more carefully,
than solo would. Earlier versions of this skill cost as much as solo or more,
and the logs showed why: the team did three to six times the work. Everything
below exists to stop that.

## Where the money goes

Every request an agent makes re-sends its entire context. So an agent costs
roughly **steps × context size**, and because context grows with every step,
a long-running agent's cost grows with the square of its length.

Price of a token, lead against worker:

| | Lead writes it (output) | Lead takes it in (new context) | Replayed on each later step |
|---|---|---|---|
| Fable lead vs Sonnet worker | 4× | ~6× | 2× |
| Opus lead vs Sonnet worker | 2× | ~3× | 2× |
| Fable or Opus lead vs Haiku worker | 40–80× | 60–130× | 20× — but only 4× once the Haiku prompt passes 100k tokens |

Three things follow.

1. **The savings come from the lead not writing code and not reading files or
   tool output.** Those are the 4–6× tokens.
2. **Replay is only 2× cheaper on Sonnet.** If the team replays more than about
   twice the tokens a solo run would have, delegation loses money. The team
   has to do about the same amount of work as solo, not more.
3. **The lead's own steps are the most expensive in the system.** A lead that
   makes 150 requests and reads the code has already spent most of a solo run.

What went wrong in measured runs:

- **Work inflation.** Each worker wrote its file in one step, then spent sixty
  more building its own preview page and running a private screenshot-and-fix
  loop. Then a QA agent re-tested everything, then the lead re-tested it
  again. Four to nine times the tool calls of the solo run.
- **Long-lived workers.** 50–160 steps each, contexts of 300–750k tokens,
  resumed again and again with SendMessage.
- **A heavy lead.** 55–170 requests: a long brief per worker, a ledger, files
  and screenshots read into its own context, a reaction to every notification.

## The budget

Defaults for a medium build; scale with the job. Before adding any agent or
step, ask whether a solo run would have done that work. If not, cut it.

| | Budget |
|---|---|
| Lead | about 25 requests for the whole job; one spec; dispatch prompts of ~8 lines; no source files read after planning; a handful of images |
| Workers | as few as the job splits into cleanly — usually 2–5 builders and 1 integrator |
| Sonnet builder | 800–2,500 lines of output, about 30 steps |
| Haiku builder | one file of up to ~500 lines, about 12 steps, so its prompt stays under 100k |
| Integrator | about 45 steps |
| Fix rounds | 2 at most, each done by a fresh worker |

A step is one message, however many tool calls it carries. Batching
independent calls into one message is the cheapest optimisation there is, for
you and for workers.

## When not to delegate

A worker carries 30–50k tokens of overhead before it does anything, and you
pay to brief it and to read its report. If you could finish the job in a dozen
tool calls or a couple of hundred lines, do it yourself. Do one-line fixes you have already pinned down yourself too, and
anything that touches a secret the user gave you.

## Choosing the team

**The user's choice of worker model wins.** If the user names the model the
sub-agents should run on ("use Sonnet sub-agents", "orchestrate Sonnet"), that
model is the team for every delegated task, routine ones included. Users who
name a worker model are often comparing setups, and a team that quietly mixes
in another model breaks the comparison.

| User asked for | Worker for everything |
|---|---|
| Nothing specific | `haiku-worker` |
| Sonnet sub-agents | `sonnet-worker` |
| Haiku sub-agents | `haiku-worker` (`sonnet-worker` for one design-critical piece only if the user agrees) |
| Another model | that model's pinned agent if one exists, else the Agent tool with `model:` set |

Use the pinned agent types rather than a generic sub-agent: a generic one
inherits your model, which makes it a second lead at lead prices. The pinned
workers also carry the cost habits this skill relies on.

## The flow

### 1. Plan (1–3 requests)

Read the request and only as much of the project as you need to split the
work. In an existing codebase, a few targeted searches are cheaper than
reading files; if planning needs more than about five files, send one worker
to map the area and return a map of 40 lines or fewer.

Split the work into pieces with **disjoint files**, each sized to the builder
budget above. Prefer few, substantial pieces over many small ones: every extra
worker pays the overhead again and adds a seam that can fail at integration.

### 2. Write the spec (one file, one request)

`SPEC.md` at the project root (in a repository the user cares about, the
session scratchpad instead). Template: `references/spec-template.md`.

The spec is where your judgment gets into the build, so spend your output here
rather than anywhere else. A worker starts with none of your context and will
guess at anything left open. Decide the hard things yourself: architecture,
the tricky algorithm, the visual direction in numbers. If a piece hinges on
one hard function, write that function in the spec. For visual work, take
targets and known-bad looks from `references/review-rubric.md` and put them in
the spec, because preventing a bad look costs one sentence and fixing it costs
a round.

Two things make the rest of the flow possible:

- **Contracts.** Exact paths, exported names and signatures, data shapes,
  element ids, events, shared constants. With these pinned, pieces that depend
  on each other can be built at the same time.
- **A way to reach any state directly**, built into the product: a URL
  parameter (`?level=3`, `?t=12.5`), a CLI flag, a seed. That hook is the only
  test harness anyone needs.

### 3. Build wave (one message, all builders in parallel)

One Agent call per builder, all in the same message:

```
You are building <piece> of <project>. Project root: <path>.
Read SPEC.md sections 0–2 and section <n> (yours). Read nothing else unless the spec points to it.
You own: <files>. Touch no other file.
Check before finishing: <one cheap command: syntax check, unit tests, build>.
Budget: about <N> steps. No preview pages, harnesses, docs or screenshots; integration testing happens after all pieces land.
Report in 8 lines or fewer: files written, check result, deviations from the spec.
```

The spec carries the detail, so the prompt stays this short. Writing a long
brief per worker was a large share of the lead's cost.

Use a second wave only when a piece cannot be written sensibly without seeing
another piece's finished code. Two waves at most.

A builder whose piece is visual may take up to three looks at it in the
running product, through the state hook. Nobody builds a preview page.

While the wave runs, wait. A notification for one worker while others are
still running gets a one-line acknowledgement, not a review.

### 4. Integrate and test (one worker)

Verification happens once, here, on the assembled product, which is how a solo
run would do it. One fresh worker owns the whole tree:

```
All pieces of <project> are written (SPEC.md, root <path>). You now own every file.
Run it the way a user would and walk the acceptance criteria in SPEC.md section 1.
Fix what is broken or below the spec's targets, at the source, in whichever file it lives.
Save up to 4 screenshots of the key states to shots/ at 1280 px wide or less.
Budget: about 45 steps. Stop there and report what is left rather than pushing on.
Report in 15 lines or fewer: pass/fail per criterion, what you fixed, what remains, how to run it (URL or command).
```

### 5. Review once (lead)

Read the report and look at the evidence in one batched step: the saved
screenshots read together, or one batched browser pass, or the test output.
For a code change, read the diff, not the files. Judge against the spec's
criteria and targets. Your eye is cheap here and is worth more than another
worker's.

Then either deliver, or write one fix list: up to five defects, each with
where it is, what is wrong and what correct looks like. Group them by file so
fixers own disjoint files, and dispatch **fresh** workers:

```
Fix round on <project> (SPEC.md, root <path>). You own: <files>.
1. <where> — <what is wrong> — <what correct looks like>
2. ...
Check: <command>. Budget: about 25 steps. Report in 8 lines or fewer.
```

After the second fix round, deliver with the remaining weaknesses stated
plainly. A third round rarely pays for itself.

### 6. Deliver

Tell the user what works and the evidence for it, known weaknesses, how to run
it, and one line on the team: how many workers on which model, and the token
totals from their completion notifications.

## Rules that protect the budget

- **Never resume a finished worker.** A resumed worker re-reads its whole
  history on every step, and if it sat idle for more than five minutes that
  history is billed again as new context first. A fresh worker with a
  five-line pointer to the spec and the file starts at a tenth of the size.
- **Workers hand over rather than grow.** There is no price cliff on Sonnet,
  but a step taken at 400k tokens of context costs eight times a step taken at
  50k, so a fresh worker pays for itself within a handful of steps once a
  context passes about 200k. Haiku does have a cliff: every request over 100k
  tokens is billed at five times the rate. The step budgets keep workers below
  both, and the pinned workers back that up by compacting themselves at 200k
  (Sonnet) and 100k (Haiku) and stopping at a hard turn limit.
- **A cut-off worker is not a failure.** Its files are on disk. Dispatch a
  fresh worker with what remains.
- **Keep worker output out of your context.** Short reports; anything long goes
  in a file and you read it only if you need it.
- **No ledger, no per-worker brief files, no API docs, no design docs beyond
  the spec.** The spec's ownership table and the reports already in your
  context are the state. After an interruption, read the spec and list the
  files to see what exists.
- **One editor per file at a time.** Builders own disjoint files; the
  integrator owns everything only after the builders have finished.
- **The integrator starts any server it needs,** detached so it survives
  (`nohup … &`), and reports the URL and restart command. Builders start none.
- **Same defect in two places means it lives upstream.** Fix it in the shared
  module or the spec, once.
- **Status to the user:** one line at each step of the flow. Never report a
  result before the worker's notification arrives.

## Other shapes of job

**Bug fix or feature in an existing codebase.** Plan with searches, write a
short spec (what to change, where, how to verify), send one worker to make the
change and run the tests, then read the diff. Split across workers only when
the change spans areas that don't share files.

**Code review, audit or research.** The same shape: one spec (scope, what
counts as a finding, the output format, the areas), parallel readers that each
own a disjoint area of about 3,000 lines and write findings to
`findings/<area>.md` with file, line and evidence, each finding checked by the
reader before it is written down. Then read the findings files yourself and
verify the ones you will stake the report on by reading the cited lines.
A separate verifier per reader doubles the bill.

**Haiku teams.** Haiku is so cheap below 100k tokens of prompt that the lead
is nearly the whole bill, so the lead budget matters most. Size each Haiku
piece to finish under the threshold: one file, a spec section of 150 lines or
fewer, no screenshots, about 12 steps. Use more, smaller builders than you
would with Sonnet, and still one integrator. The integrator will pass 100k and
compact; give it a narrow checklist so it loses little when it does.

**Sonnet as the lead.** The price gap to Sonnet workers is zero, so delegate
only for parallel speed or to keep a long job's context small.

## Reference files

- `references/spec-template.md` — the spec skeleton and a sanity pass to run
  before dispatching.
- `references/review-rubric.md` — acceptance checks, numeric targets and a
  catalogue of known-bad looks. Read it while writing the spec for visual or
  interactive work, and again at the review.
