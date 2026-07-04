---
tags: dashboard
---

# 🎛️ TaskView — Mission Control

Search, filter, and sort every task in `99 Archives/Tasks.md` from one place.
Completing a task here just *displays* status — to check something off, do it from the **Daily Note**, from a **project note**, or by opening [[Tasks]] directly. (Why: see the Setup Guide → "Why isn't this table clickable?")

> ⚙️ Requires Dataview's **"Enable JavaScript Queries"** setting to be turned on (Settings → Dataview).

```dataviewjs
// ---- CONFIG (change these two lines if your paths ever change) ----
const TASKS_FILE = "99 Archives/Tasks.md";
const DATE_FORMAT = "dd-MM-yyyy";

// ---- Priority emoji (Tasks plugin's real set — Dataview can't read these natively, so we parse them ourselves) ----
const PRIORITY_EMOJI = ["🔺", "⏫", "🔼", "🔽", "⏬"];
const PRIORITY_LABEL = { "🔺": "Highest", "⏫": "High", "🔼": "Medium", "": "Normal", "🔽": "Low", "⏬": "Lowest" };
const PRIORITY_RANK  = { "🔺": 1, "⏫": 2, "🔼": 3, "": 4, "🔽": 5, "⏬": 6 };

function getPriorityEmoji(text) {
  for (const e of PRIORITY_EMOJI) if (text.includes(e)) return e;
  return "";
}

function getArea(tags) {
  if (!tags || tags.length === 0) return "untagged";
  const raw = String(tags[0]).replace("#", "");
  return raw.split("/")[0] || "untagged";
}

function cleanText(text) {
  return text
    .replace(/📅\s*\d{4}-\d{2}-\d{2}/g, "")
    .replace(/➕\s*\d{4}-\d{2}-\d{2}/g, "")
    .replace(/✅\s*\d{4}-\d{2}-\d{2}/g, "")
    .replace(/[🔺⏫🔼🔽⏬]/g, "")
    .replace(/#\S+/g, "")
    .replace(/\s{2,}/g, " ")
    .trim();
}

// ---- Load tasks ----
const page = dv.page(TASKS_FILE);
const rawTasks = (page && page.file && page.file.tasks) ? page.file.tasks.array() : [];

if (!page) {
  dv.paragraph(`⚠️ Couldn't find **${TASKS_FILE}**. Make sure the file exists at that exact path.`);
}

const tasks = rawTasks.map(t => {
  const pe = getPriorityEmoji(t.text);
  return {
    display: cleanText(t.text) || "(empty)",
    area: getArea(t.tags),
    due: t.due ? t.due.toFormat(DATE_FORMAT) : "",
    dueSort: t.due ? t.due.toMillis() : Infinity,
    created: t.created ? t.created.toFormat(DATE_FORMAT) : "",
    createdSort: t.created ? t.created.toMillis() : -Infinity,
    priorityLabel: PRIORITY_LABEL[pe],
    priorityRank: PRIORITY_RANK[pe],
    tags: (t.tags || []).join(" "),
    completed: t.completed,
  };
});

// ---- UI state ----
let state = { search: "", area: "all", priority: "all", showDone: false, sortKey: "due", sortDir: "asc" };

const root = dv.container.createEl("div", { cls: "taskflow-view" });

root.createEl("style", { text: `
.taskflow-controls { display: flex; gap: 8px; margin-bottom: 10px; flex-wrap: wrap; align-items: center; }
.taskflow-controls input[type="text"] { flex: 1; min-width: 180px; padding: 5px 8px; border-radius: 4px; border: 1px solid var(--background-modifier-border); background: var(--background-primary); color: var(--text-normal); }
.taskflow-controls select { padding: 5px 8px; border-radius: 4px; border: 1px solid var(--background-modifier-border); background: var(--background-primary); color: var(--text-normal); }
.taskflow-controls label { display: flex; align-items: center; gap: 4px; font-size: 0.9em; color: var(--text-muted); }
.taskflow-table { width: 100%; border-collapse: collapse; margin-top: 4px; }
.taskflow-table th, .taskflow-table td { border: 1px solid var(--background-modifier-border); padding: 6px 9px; text-align: left; font-size: 0.88em; }
.taskflow-table th { background: var(--background-secondary); user-select: none; white-space: nowrap; }
.taskflow-table tr:hover td { background: var(--background-secondary-alt); }
.taskflow-count { color: var(--text-muted); font-size: 0.85em; margin: 6px 0; }
`});

const controls = root.createEl("div", { cls: "taskflow-controls" });
const searchInput = controls.createEl("input", { attr: { type: "text", placeholder: "Search task text or tags…" } });

const areaSelect = controls.createEl("select");
const areas = ["all", ...Array.from(new Set(tasks.map(t => t.area))).sort()];
for (const a of areas) areaSelect.createEl("option", { text: a === "all" ? "All areas" : a, attr: { value: a } });

const prioritySelect = controls.createEl("select");
const priorities = ["all", "Highest", "High", "Medium", "Normal", "Low", "Lowest"];
for (const p of priorities) prioritySelect.createEl("option", { text: p === "all" ? "All priorities" : p, attr: { value: p } });

const doneLabel = controls.createEl("label");
const doneCheckbox = doneLabel.createEl("input", { attr: { type: "checkbox" } });
doneLabel.appendText("Show completed");

const countEl = root.createEl("div", { cls: "taskflow-count" });
const tableContainer = root.createEl("div");

function render() {
  tableContainer.empty();

  let filtered = tasks.filter(t => {
    if (!state.showDone && t.completed) return false;
    if (state.area !== "all" && t.area !== state.area) return false;
    if (state.priority !== "all" && t.priorityLabel !== state.priority) return false;
    if (state.search) {
      const hay = (t.display + " " + t.tags).toLowerCase();
      if (!hay.includes(state.search)) return false;
    }
    return true;
  });

  const sortKeyMap = { task: "display", area: "area", due: "dueSort", created: "createdSort", priority: "priorityRank", tags: "tags", status: "completed" };
  const key = sortKeyMap[state.sortKey] || "dueSort";
  filtered.sort((a, b) => {
    let av = a[key], bv = b[key];
    if (typeof av === "string") { av = av.toLowerCase(); bv = bv.toLowerCase(); }
    if (av < bv) return state.sortDir === "asc" ? -1 : 1;
    if (av > bv) return state.sortDir === "asc" ? 1 : -1;
    return 0;
  });

  countEl.setText(`${filtered.length} of ${tasks.length} tasks`);

  const headers = [
    { key: "task", label: "Task" },
    { key: "area", label: "Area" },
    { key: "due", label: "Due" },
    { key: "created", label: "Created" },
    { key: "priority", label: "Priority" },
    { key: "tags", label: "Tags" },
    { key: "status", label: "Status" },
  ];

  const table = tableContainer.createEl("table", { cls: "taskflow-table" });
  const thead = table.createEl("thead");
  const headRow = thead.createEl("tr");
  for (const h of headers) {
    const arrow = state.sortKey === h.key ? (state.sortDir === "asc" ? " ▲" : " ▼") : "";
    const th = headRow.createEl("th", { text: h.label + arrow });
    th.style.cursor = "pointer";
    th.onclick = () => {
      if (state.sortKey === h.key) state.sortDir = state.sortDir === "asc" ? "desc" : "asc";
      else { state.sortKey = h.key; state.sortDir = "asc"; }
      render();
    };
  }

  const tbody = table.createEl("tbody");
  if (filtered.length === 0) {
    const row = tbody.createEl("tr");
    row.createEl("td", { text: "No tasks match these filters.", attr: { colspan: "7" } });
  }
  for (const t of filtered) {
    const row = tbody.createEl("tr");
    row.createEl("td", { text: t.display });
    row.createEl("td", { text: t.area });
    row.createEl("td", { text: t.due || "—" });
    row.createEl("td", { text: t.created || "—" });
    row.createEl("td", { text: t.priorityLabel });
    row.createEl("td", { text: t.tags || "—" });
    row.createEl("td", { text: t.completed ? "✅ Done" : "⬜ Open" });
  }
}

searchInput.oninput = (e) => { state.search = e.target.value.toLowerCase(); render(); };
areaSelect.onchange = (e) => { state.area = e.target.value; render(); };
prioritySelect.onchange = (e) => { state.priority = e.target.value; render(); };
doneCheckbox.onchange = (e) => { state.showDone = e.target.checked; render(); };

render();
```
