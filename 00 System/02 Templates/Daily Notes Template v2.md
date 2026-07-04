---
tags: daily
---

# {{date}}

---

## ➕ Add a Task

Put your cursor on a blank line in **[[Tasks]]** (`99 Archives/Tasks.md`) and run:

> `Tasks: Create or edit task` (Cmd/Ctrl + P)

The modal fills in the due date, priority and tag-safe formatting for you. Created date is added automatically once you've enabled that setting (see the Setup Guide).

---

## 📌 Due Today

```tasks
path includes 99 Archives/Tasks.md
due today
not done
sort by priority
```

---

## 🆕 Created Today

```tasks
path includes 99 Archives/Tasks.md
created today
not done
```

> If this section ever comes up empty when it shouldn't, try changing `created today` to `created on today` — both should work, but if the Tasks plugin update you're on prefers one phrasing, this is the line to tweak.

---

## 🎯 Extra Tasks (if today's work is done)

A small, non-overwhelming sample of tasks with no due date — pick one if you've got energy left.

```tasks
path includes 99 Archives/Tasks.md
no due date
not done
limit 5
sort by priority
```

---

## 📝 Notes

