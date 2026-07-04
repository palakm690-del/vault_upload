---
tags: config
---

# ⚙️ TaskFlow Config

One place for every convention the system depends on. If you ever change one of these, this is the file to update first — then check `Tasks.md`, `TaskView.md`, and the Daily Notes template for anywhere it's referenced.

## File locations

| Thing | Path |
|---|---|
| Task dump (single source of truth) | `99 Archives/Tasks.md` |
| Mission control dashboard | `00 System/00 Dashboards/TaskView.md` |
| Daily note template | `00 System/01 Templates/Daily Notes Template.md` |

## Ignored / infrastructure folders

These are never treated as "work areas" for tagging purposes:

```
00 System
99 Archives
```

(Anything under them — Dashboards, Templates, Scripts, Config, Assets, Excalidraw — is infrastructure, not a project area.)

## Date format

- **On the task line itself:** `YYYY-MM-DD` — this is hard-required by the Tasks plugin; it will not parse anything else.
- **Displayed in TaskView:** `DD-MM-YYYY` — handled automatically by the dashboard script.
- **Displayed in Daily Note / Tasks plugin lists:** shown in the Tasks plugin's own native rendering. There's currently no way to force the Tasks plugin itself to render dates as `DD-MM-YYYY` — only Dataview-rendered views (like TaskView) can be reformatted.

## Priority symbols (Tasks plugin's real set)

| Symbol | Meaning | Use it for… |
|---|---|---|
| 🔺 | Highest | Truly urgent |
| ⏫ | High | *(optional — skip if you only want 3 tiers)* |
| 🔼 | Medium | *(optional)* |
| *(blank)* | Normal | Default — most tasks |
| 🔽 | Low | Can slip |
| ⏬ | Lowest | *(optional)* |

If you want the simple 3-tier system from the original plan: use **🔺** for high, **leave blank** for normal, **🔽** for low.

## Tag structure

```
#area/subarea/topic
```

- Mirrors your top-level work folders (College, Programming, Internship, Research, Creative, Personal).
- Lowercase, no spaces: `Video Editing` → `videoediting`, `C++` → `cpp`, `C#` → `csharp`, `Semester 3` → `semester3`.
- Max depth: **6 levels** (your call — most tasks will only need 2–3).
- One tag per task. Typed manually when you create the task — this build does not auto-generate tags from folders (see Setup Guide for why).

## Folders considered "work areas"

Dynamic, not fixed — currently:

```
College
Programming
Internship
Research
Creative
Personal
```

These map to the first segment of a task's tag (`#college/...`, `#programming/...`, etc.) — TaskView's "Area" column is computed from this segment automatically.
