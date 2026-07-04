# TaskFlow Setup Guide

This covers exactly four things, the way you asked: the task dump, TaskView, the Daily Note sections, and tag-based embedding in project notes — plus the settings you need to flip for any of it to work.

---

## First: your question

> *"Master task inbox" vs "zero duplicate checkboxes" — what's the difference?*

They sound like the same idea but they're solving two different problems:

- **Master task inbox** is about *where tasks live* — one file, instead of scattered across a dozen project notes. This is a filing decision.
- **Zero duplicate checkboxes** is about *how many physical copies of a task's checkbox exist in your vault* — the answer should always be exactly one, no matter how many places you can *see* it. This is a data-integrity decision.

You can have a master inbox and still end up with duplicate checkboxes — e.g. if you wrote a task in `Tasks.md` and then also pasted `- [ ] Finish DSA Assignment` into `DSA.md` so you'd see it there too. Now there are two checkboxes. Check one off, and the other one is still sitting there unchecked, out of sync, forever.

The fix is that `DSA.md` never gets its own checkbox. It gets a **query** (a `tasks` code block filtered to `#programming/dsa`) that *reads* the one real checkbox living in `Tasks.md` and displays it live. Click it in `DSA.md`, and Tasks plugin edits the original line in `Tasks.md` — because there's only ever one line to edit. That's what makes "zero duplicates" possible: the master inbox is the *mechanism*, zero duplicates is the *guarantee* it gives you.

---

## Settings to flip (one-time, ~2 minutes)

1. **Tasks plugin** → Settings → Tasks → turn on **"Set created date on every added task"**. Restart Obsidian after. This is what makes the ➕ created date appear automatically, on every task, whether you type it by hand or use the modal.
2. **Dataview** → Settings → Dataview → turn on **"Enable JavaScript Queries"**. Required for TaskView's interactive table to render at all.

That's it — no QuickAdd or Meta Bind configuration needed for what's in this build (more on that below).

---

## The four pieces

### 1. `Tasks.md` (`99 Archives/Tasks.md`)
A dump. Nothing else. To add a task: put your cursor on a blank line in this file and run **`Tasks: Create or edit task`** from the command palette (Cmd/Ctrl+P). The modal gives you a description field, a due-date picker (understands "next Friday" etc.), and priority radio buttons, and writes a correctly-formatted line for you — so you never have to memorize emoji order by hand.

Example of what comes out the other end:
```
- [ ] Finish DSA Assignment 📅 2026-07-01 ➕ 2026-06-29 🔺 #college/sem3/dsa
```
(Tag is typed in manually at the end — see below.)

### 2. `TaskView.md` (`00 System/00 Dashboards/TaskView.md`)
An interactive table: search box, Area dropdown, Priority dropdown, "show completed" toggle, and clickable column headers to sort. It reads directly from `Tasks.md`.

**Why isn't this table clickable to complete tasks?** Dataview *can* render a clickable checklist, but not a clickable multi-column table — those are two different rendering modes in the plugin. Building a fake clickable checkbox that writes back to the file is fragile and easy to break with a plugin update, so this build keeps TaskView as a read/filter/sort surface, and leaves the actual checking-off to the Tasks-plugin-rendered lists (Daily Note, project notes, or `Tasks.md` itself) — which are natively interactive and far more reliable.

### 3. Daily Note template (`00 System/01 Templates/Daily Notes Template.md`)
Three sections — Due Today, Created Today, Extra Tasks — each a small `tasks` query block scoped to `Tasks.md`. Point Periodic Notes at this template and your daily note auto-fills these sections every time you open today's note (creating it from the template if it doesn't exist yet).

### 4. Tag-based embedding in project notes
Drop this pattern into any project note (e.g. `DSA.md`) to show only the tasks tagged for that project, fully interactive:

```tasks
path includes 99 Archives/Tasks.md
tags include #college/sem3/dsa
not done
sort by priority
```

Check one off here, and it's checked off everywhere — same underlying line. Add a second block with `done` instead of `not done` if you want a "completed" section on the same page.

---

## Tag convention (your decision, confirmed)

`#area/subarea/topic` — up to 6 levels, lowercase, no spaces. Full reference in `Config.md`. Since this build doesn't auto-generate tags from folders, you type the tag yourself when you create each task (it's one extra short word at the end of a line you're already typing in the modal's description field, or appended after).

---

## What's deliberately *not* in this build

You asked for exactly the four things above, so this skips the parts of the original mega-spec that weren't in that list: the QuickAdd capture wizard, a Meta Bind "➕ Add Task" button, automatic tag generation from a folder picker, unique task IDs, and the Weekly Review dashboard. None of that is hard to add later — just say which one and I'll build it the same way: verified against the actual plugin docs first, not invented from memory.
