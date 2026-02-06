# Product Requirements Document (PRD) - TODO App Enhancements

## 1. Overview

We are upgrading the basic TODO app (currently supporting only a title and completed flag) to better help users manage urgency and focus. The enhanced app will support due dates, simple priorities, and basic filtering so users can quickly see what is due today or overdue, without adding backend complexity. The goal is a simple, teachable MVP that runs entirely on local storage, with additional visual and sorting enhancements reserved for post-MVP.

---

## 2. MVP Scope

- **Data model (frontend / local):**
  - `title`: required text field for each task.
  - `completed`: existing boolean indicating if a task is done.
  - `priority`: enum string field with values `"P1" | "P2" | "P3"`, defaulting to `"P3"` when not specified.
  - `dueDate`: optional date field in ISO `YYYY-MM-DD` format.
  - Invalid `dueDate` values must be safely ignored and treated as if `dueDate` is absent.
- **Task creation / editing:**
  - Users can set or change `priority` for a task, with `P3` as the default when creating a new task.
  - Users can optionally set or clear a `dueDate` using a simple date input.
- **Views / filters:**
  - Provide three primary views (e.g., as tabs or buttons): **All**, **Today**, **Overdue**.
  - **All** view:
    - Shows all tasks, including completed and incomplete, regardless of due date.
  - **Today** view:
    - Shows tasks that are due on the current calendar date and are **not completed**.
  - **Overdue** view:
    - Shows tasks with a `dueDate` earlier than today that are **not completed**.
- **Priority display:**
  - Each task displays its `priority` value (P1, P2, or P3) in the UI in a clear, easily readable way (e.g., simple label or badge).
  - Visual styling for priorities can be basic in MVP (no strict color requirements beyond being distinguishable).
- **Filtering behavior with completion:**
  - In **All** view, completed tasks remain visible.
  - In **Today** and **Overdue** views, completed tasks are excluded; only incomplete tasks are shown.
- **Storage and architecture:**
  - All task data (including `priority` and `dueDate`) is stored locally on the client (e.g., in-memory or browser local storage).
  - No new backend endpoints, databases, or external storage services are introduced for MVP.
- **Non-goals / simplifications (MVP-specific):**
  - No special keyboard navigation or accessibility work beyond the framework defaults.
  - No notifications, reminders, or recurring task logic.

---

## 3. Post-MVP Scope

- **Visual highlighting for overdue tasks:**
  - Overdue tasks are visually distinguished so they stand out clearly (e.g., red text, border, or background treatment).
  - Highlighting applies wherever overdue tasks are rendered (e.g., All and Overdue views).
- **Sorting behavior across lists:**
  - Implement a consistent sort order for all task lists:
    - Overdue tasks appear first.
    - Within the same overdue / non-overdue grouping, tasks are ordered by `priority`: `P1` before `P2`, `P2` before `P3`.
    - Within the same priority, tasks are ordered by `dueDate` ascending (earlier dates first).
    - Tasks with no `dueDate` appear last in their respective priority grouping.
- **Refined priority visuals:**
  - Priority badges adopt a clearer visual system, such as:
    - P1: red badge
    - P2: orange badge
    - P3: gray badge
  - Exact colors and design can be tuned but should clearly communicate relative urgency.

---

## 4. Out of Scope

- Any form of **notifications** or reminders (email, push, in-app alerts).
- **Recurring tasks** (e.g., daily/weekly repetition, templates).
- **Multi-user** features (authentication, user accounts, shared lists).
- Advanced **keyboard navigation** and specialized accessibility work beyond default browser/framework behavior.
- Any **backend** or **external storage** changes:
  - No new APIs or database schemas.
  - No cloud storage or syncing across devices.
- Additional advanced views or filters beyond **All**, **Today**, and **Overdue**, unless explicitly added in a future scope.
