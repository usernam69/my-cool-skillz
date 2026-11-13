---
name: gcalendar
description: Read, create, and manage Google Calendar events. Use when the user shares a calendar.google.com URL, asks what is on their schedule, wants an event found, created, updated, deleted, or moved, wants to RSVP, wants free/busy or scheduling conflicts checked before proposing a time, wants focus time or an out-of-office block created, wants an event quick-added from a phrase or imported from an ICS file, or wants to know where someone is working from and which timezone they are in. Don't use for Gmail, Chat, or Tasks.
---

# Google Calendar

Work with the calendars and events the user names or links to. Commands that act
on a single event take an **event ID**; commands that act on a calendar take a
**calendar ID**, which is an email address for a person or a room.

> `mutate delete` removes an event immediately. There is no confirmation prompt,
> no `--confirm` flag, and no undo. **Never** delete an event unless the user
> explicitly asked you to delete that specific event, and say which event you
> are about to remove before you run it.
>
> **Calendar writes reach other people.** Creating, updating, moving, or
> deleting an event that has guests can email all of them, and an invitation
> cannot be recalled. Confirm the title, time, and guest list with the user
> before you write, rather than fixing it afterwards.
>
> **Never invite anyone the user did not name**, and never widen an event's
> visibility on your own initiative.

## Setup

Run this skill's script once, before anything else. It downloads the CLI for
this machine, registers `gcalendar` on the agent's `PATH` (not the user's
shell), and prints the version:

```bash
<path/to/this/skill>/scripts/gcalendar --version
```

```powershell
# Windows (PowerShell)
& "<path\to\this\skill>\scripts\gcalendar.cmd" --version
```

After that, always run bare `gcalendar`, never the script by its path: only
`gcalendar` gets the user's sign-in. Never ask the user to run it; they cannot.

## Authentication

Run bare `gcalendar` and let the result tell you how sign-in works. Don't search
the filesystem or environment for credentials, and don't ask the user for a
token.

-   **Antigravity manages sign-in** when a command fails with Antigravity's
    "needs authentication" message. Follow it: show the plugin card it gives and
    ask the user to click **Sign in** on it, then retry once they say they have
    signed in. Do not run `gcalendar auth login` in this case. If more than one
    account is connected, choose one by prefixing the command with
    `AGY_ACCOUNT=<account>`, for example
    `AGY_ACCOUNT=me@example.com gcalendar ...`. In PowerShell, set it first:
    `$env:AGY_ACCOUNT = "me@example.com"; gcalendar ...`.
-   **The CLI manages sign-in** when it says
    `no credentials found: run gcalendar auth login`. This version of
    Antigravity doesn't sign in for this plugin, so log in for the user: start
    `gcalendar auth login` as a background command. It prints an authorization
    URL and exits on its own once the user approves. Read the URL from the
    task's log, open it in the user's browser or hand them the link, then stop
    and wait. Confirm with `gcalendar auth status`, then retry.
-   A 403 that names a missing permission or scope is not a sign-in problem.
    Report it instead of asking the user to sign in again.

## Usage

Do every Calendar operation — listing, searching, creating, updating, RSVPing —
through `gcalendar`. Every command sits under one of two namespaces:

-   `readonly` lists and searches events and checks availability without
    changing anything.
-   `mutate` creates, updates, deletes, and RSVPs to events.

So the commands read `gcalendar readonly events` and `gcalendar mutate create`.
Flat forms like `gcalendar events` still work but are deprecated and print a
migration warning; always write the namespaced form.

### Read the calendar before you write to it

> [!IMPORTANT]
>
> A time that looks free in the user's request is often already taken, and
> double-booking is the failure they will notice. Before creating or moving
> anything:
>
> 1.  Look at the day with `readonly events --date <date> --max 50 --json`.
> 2.  Check every invitee with `readonly freebusy --email ... --start ... --end
>     ...`, passing the range explicitly.
> 3.  Only then create the event, and report back the `Link:` the command
>     prints.
>
> The same applies to identity: run `readonly calendars` when the user refers to
> a calendar by name ("my team calendar") rather than guessing an ID, and
> `readonly get EVENT_ID` before editing an event you have not already read in
> this session.

### Times and timezones

> [!IMPORTANT]
>
> Timestamps are RFC3339 (`2025-01-15T10:00:00Z` or `2025-01-15T10:00:00-08:00`)
> and dates are `YYYY-MM-DD`. When neither `--timezone` nor an offset is given,
> the CLI uses the timezone from the user's Calendar settings.
>
> **Resolve relative dates before you build a command.** "Tomorrow", "next
> Tuesday", and "in two weeks" have to become concrete dates in the user's
> timezone first; never paste a relative phrase into `--start`. Use `readonly
> workloc` if you need to know which timezone a colleague is actually in.
>
> **All-day events end on an exclusive date.** `--all-day --start 2025-01-15
> --end 2025-01-16` is one day, the 15th. The CLI passes the dates straight
> through and will not add the extra day for you.

### Reporting back

Mutating commands print a `Link:` line for the event they created. Always pass
that URL through to the user so they can check the result, and quote the event
ID for anything they may want to change next.

Where a command prints nothing useful — `mutate update`, for instance, prints no
link — follow up with `readonly get EVENT_ID` rather than telling the user the
change "should" have worked.

## Recipes

See [references/recipes.md](references/recipes.md) for worked examples covering
viewing a schedule, creating and editing events, finding a slot that works for a
group, and determining when someone on leave is back.

## Commands

The essentials are below. For every flag of every command, including the
defaults that are easy to get wrong, see
[references/commands.md](references/commands.md).

Command                  | Description
------------------------ | -----------
`readonly events`        | List events over a date or a range
`readonly today`         | Today's events
`readonly search`        | Search events by keyword
`readonly get`           | Full details for one event
`readonly instances`     | Occurrences of a recurring event
`readonly conflicts`     | Overlapping events today
`readonly calendars`     | List calendars and their IDs
`readonly colors`        | Color IDs for `--color`
`readonly acl`           | Who a calendar is shared with
`readonly freebusy`      | Busy intervals for one or more people
`readonly next-free`     | First open slot on the user's own calendar
`readonly working-hours` | Calendar settings, including timezone and working hours
`readonly workloc`       | Where someone is working from
`mutate create`          | Create an event
`mutate update`          | Update an event
`mutate recurring`       | Create a recurring series from an RRULE
`mutate focus-time`      | Block out solo focus time
`mutate quick-add`       | Create an event from a natural-language phrase
`mutate rsvp`            | Respond to an invitation
`mutate move`            | Move an event to another calendar
`mutate delete`          | Delete an event
`mutate import-ics`      | Import an event from an ICS file
`mutate workloc-set`     | Set your working location

### Notifications

`--send-updates` controls who gets emailed, and it defaults to `none` on
`create`, `update`, `recurring`, and `delete`. That default is the safe one, but
it also means **guests are not told about an event you create or cancel** unless
you opt in with `--send-updates all`.

Decide deliberately and say which you chose: silent for the user's own blocks
and drafts, `all` when the user is actually inviting people or cancelling on
them.

### Meetings with other people

-   `--attendee` and `--optional-attendee` take one address each and repeat.
-   `--gvc` attaches a new Google Meet conference; `--meet-code` attaches one
    that already exists. They are mutually exclusive.
-   On `mutate update`, attendee flags **merge** into the existing guest list
    rather than replacing it. Use `--remove-attendee` to drop someone, matching
    the address exactly.
-   `--location` is free text and **does not book a room**. Rooms are calendar
    resources that have to be invited as attendees, so pass the room's resource
    address to `--attendee`. This CLI cannot search for rooms or check their
    availability by name; ask the user for the address, or check a candidate
    with `readonly freebusy --email <room address>`.

### Blocking time out

-   `mutate focus-time` creates a solo block. It takes no attendees, and
    `--duration` (default `1h`) applies from now unless you give `--start`.
-   `mutate create --ooo` creates an Out of Office event that can auto-decline
    conflicting invitations. `--ooo-message` and `--ooo-decline-mode` only work
    alongside `--ooo`.

## Working Location

`readonly workloc` and `mutate workloc-set` read and write where someone is
working from — office, home, or a custom location — which is also the most
reliable way to tell which timezone a colleague is in today.

See [references/working_location.md](references/working_location.md) for the
details, including the rule that a one-off location overrides a recurring one
and the reasons an empty result is ambiguous.

> [!WARNING]
>
> Working location is not time off. Someone with a recurring "office" location
> who is on vacation still reports as "office"; check `readonly freebusy` over
> the same period before telling the user that a colleague is around.

## Global Flags

-   `--json` — emit JSON instead of a table. Use it whenever you are going to
    parse the output or quote exact times, because the table view truncates.
-   `--timezone` — IANA timezone override, e.g. `America/Los_Angeles`. Defaults
    to the timezone in the user's Calendar settings.
-   `--legacy-mode` — how deprecated flat commands are handled: `allowed`,
    `deprecated` (default), or `banned`.
-   `-v`, `--version` — print the CLI build version.
