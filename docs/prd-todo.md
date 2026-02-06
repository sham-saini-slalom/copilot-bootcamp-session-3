# Product Requirements Document (PRD) - TODO App Enhancement: Due Dates, Priorities & Filters

## 1. Overview

We are upgrading the basic TODO app to support due dates, priorities, and filters so users can better organize and manage their tasks. The current app only supports task title and completion status. This enhancement will add optional due dates, a three-level priority system, and filter views to help users focus on what's urgent and important without overcomplicating the application.

---

## 2. MVP Scope

- **Due Date Field**
  - Add optional `dueDate` field using ISO format `YYYY-MM-DD`
  - Invalid date values should be ignored and treated as absent
  
- **Priority Field**
  - Add `priority` enum with three levels: `P1`, `P2`, `P3`
  - Default to `P3` when not specified
  - Display priority level for each task

- **Filter Views**
  - Implement three filter tabs: **All**, **Today**, **Overdue**
  - **All** view: Show all tasks including completed ones
  - **Today** view: Show only incomplete tasks due today
  - **Overdue** view: Show only incomplete tasks with past due dates

- **Data Storage**
  - Keep storage local only (no backend or external storage)
  
- **Data Model & Validation**
  - `title`: required field
  - `priority`: `"P1" | "P2" | "P3"`, default `"P3"`
  - `dueDate`: optional ISO `YYYY-MM-DD` format; invalid values ignored

---

## 3. Post-MVP Scope

- **Visual Highlighting**
  - Overdue tasks visually highlighted (e.g., red highlighting to make them stand out)
  - Color-coded priority badges (Red for P1, Orange for P2, Gray for P3)

- **Sorting Logic**
  - Sort tasks by the following order:
    1. Overdue tasks first
    2. Then by priority (P1 → P2 → P3)
    3. Then by due date (ascending)
    4. Tasks without due dates last

---

## 4. Out of Scope

- Notifications
- Recurring tasks
- Multi-user support
- Keyboard navigation
- External storage or backend integration
- Special accessibility features
