---
tags: dataset
---

# 📊 Pattern Dataset

This is a **single page**, not a template — create it once in your vault (e.g. at the root, or in a "Dashboard" folder). It stays put and just keeps pulling in new data as you log daily notes.

Everything below is pulled live from daily note frontmatter. Nothing here needs to be typed twice — log once in the daily note, it shows up here automatically.

## Full log history
```dataview
table sleep_time as "Slept", wake_time as "Woke", deep_work_hours as "Deep Work (hrs)", moved_forward as "Moved Forward", friction as "Friction", off_plan as "Off-Plan"
from "Daily Notes"
sort date asc
```

## Copying this to Claude
Dataview renders this as a live table in Obsidian's preview, but "copy as text" on a rendered table doesn't always paste back into chat as clean markdown. Two reliable options:

1. In Obsidian, right-click the rendered table → **Copy as Markdown** (if your Obsidian version supports it), then paste directly into chat.
2. If that option isn't available, switch this note to source view before copying — the raw dataview query block won't help you, but you can temporarily export via **Dataview → "Copy query results"** (community plugin command) or just manually skim the last N days and type a compact version if the table is small.

Either way — paste the resulting table (or a screenshot, if formatting breaks) into this chat and ask "what patterns do you see so far."
