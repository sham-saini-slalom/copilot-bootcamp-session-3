# Epics and Stories - TODO App Enhancement

## MVP Requirements

- Epic: Due Date Management
  - Story: Add due date field to task data model
    - Acceptance Criteria:
      - Due date field is optional
      - Uses ISO format YYYY-MM-DD
      - Can be null/undefined for tasks without due dates
    - Technical Requirements:
      - SQLite schema in `packages/backend/src/app.js` already has `due_date DATE` column
      - Ensure backend API endpoints (POST, PUT) accept and return `due_date` field
      - Update frontend task state in `packages/frontend/src/TaskList.js` to handle `due_date` property
      - Store as `null` when no date provided
  
  - Story: Add due date input to task form
    - Acceptance Criteria:
      - Date input field added to task creation form
      - Date input field added to task edit form
      - Input accepts ISO YYYY-MM-DD format
    - Technical Requirements:
      - Add Material UI `TextField` with `type="date"` to `packages/frontend/src/TaskForm.js`
      - Set `InputLabelProps={{ shrink: true }}` for proper date rendering
      - Add `dueDate` state variable and setter
      - Use `normalizeDateString` helper function (already implemented) to format to YYYY-MM-DD
      - Pass `due_date` in request body to backend API
  
  - Story: Display due date in task list
    - Acceptance Criteria:
      - Due date shown for tasks that have one
      - Due date displayed in readable format
      - Tasks without due date show no date indicator
    - Technical Requirements:
      - Implement `formatDueDate` function in `packages/frontend/src/TaskList.js` (already exists)
      - Use Material UI `Chip` component with `EventIcon` for display
      - Parse date as local to avoid timezone issues: `new Date(year, month - 1, day)`
      - Format as locale string: `toLocaleDateString('en-US', { year: 'numeric', month: 'short', day: 'numeric' })`
      - Conditional rendering: only show Chip when `task.due_date` is truthy
  
  - Story: Validate due date format (ISO YYYY-MM-DD)
    - Acceptance Criteria:
      - Invalid date formats are ignored
      - Invalid dates treated as absent
      - Valid ISO YYYY-MM-DD dates accepted and stored
    - Technical Requirements:
      - Add regex validation `/^\d{4}-\d{2}-\d{2}$/` in `packages/frontend/src/TaskForm.js`
      - Backend accepts `null` or valid ISO date string in POST/PUT endpoints
      - Use `due_date || null` in SQL INSERT/UPDATE statements to ensure null for invalid values
      - No error thrown for invalid dates, silently store as null

- Epic: Priority System
  - Story: Add priority field to task data model
    - Acceptance Criteria:
      - Priority field accepts P1, P2, P3 values
      - Priority field stored with task data
      - Priority is a required field
    - Technical Requirements:
      - Add `priority TEXT DEFAULT 'P3'` column to tasks table in `packages/backend/src/app.js`
      - Modify CREATE TABLE statement to include priority field
      - Update POST endpoint to accept `priority` field with validation
      - Update PUT endpoint to accept `priority` field with validation
      - Update GET endpoints to return `priority` in task objects
  
  - Story: Add priority selector to task form
    - Acceptance Criteria:
      - Priority dropdown/selector in create form
      - Priority dropdown/selector in edit form
      - Shows P1, P2, P3 options
    - Technical Requirements:
      - Add Material UI `Select` component to `packages/frontend/src/TaskForm.js`
      - Create `priority` state variable initialized to 'P3'
      - Create three MenuItem options with values: 'P1', 'P2', 'P3'
      - Include `priority` in form submission payload to backend
      - Pre-populate selector with `initialTask.priority` when editing
  
  - Story: Display priority level for each task
    - Acceptance Criteria:
      - Priority displayed for all tasks
      - Shows P1, P2, or P3 label
      - Priority visible in task list
    - Technical Requirements:
      - Add priority badge in `packages/frontend/src/TaskList.js` ListItem
      - Use Material UI `Chip` component for priority display
      - Position next to due date Chip in task list
      - Apply styling consistent with Material UI theme
  
  - Story: Set default priority to P3
    - Acceptance Criteria:
      - New tasks default to P3 if not specified
      - Default value pre-selected in form
      - P3 used when priority is missing or invalid
    - Technical Requirements:
      - Set default value 'P3' in `packages/frontend/src/TaskForm.js` state: `useState('P3')`
      - Add `DEFAULT 'P3'` constraint in SQLite schema
      - Backend validation: `const priority = ['P1', 'P2', 'P3'].includes(req.body.priority) ? req.body.priority : 'P3'`
      - Select component shows 'P3' pre-selected when creating new task

- Epic: Filter Views
  - Story: Create filter tabs UI (All, Today, Overdue)
    - Acceptance Criteria:
      - Three filter tabs visible: All, Today, Overdue
      - Only one tab active at a time
      - Visual indication of active tab
    - Technical Requirements:
      - Add Material UI `Tabs` and `Tab` components to `packages/frontend/src/App.js`
      - Create state: `const [activeFilter, setActiveFilter] = useState('all')`
      - Three Tab components with values: 'all', 'today', 'overdue'
      - Style active tab with primary color (#1976d2)
      - Position tabs above TaskList component within Container
      - Pass `activeFilter` as prop to TaskList component
  
  - Story: Implement "All" filter to show all tasks
    - Acceptance Criteria:
      - Shows all tasks regardless of status
      - Includes completed tasks
      - Includes tasks with and without due dates
    - Technical Requirements:
      - When `activeFilter === 'all'`, fetch `/api/tasks` without filters
      - No client-side filtering applied
      - Display all tasks returned from backend in `packages/frontend/src/TaskList.js`
      - Default filter state
  
  - Story: Implement "Today" filter to show incomplete tasks due today
    - Acceptance Criteria:
      - Shows only incomplete tasks
      - Shows only tasks due today (current date)
      - Completed tasks excluded from view
    - Technical Requirements:
      - Implement client-side filter in `packages/frontend/src/TaskList.js`:
        ```javascript
        const today = new Date().toISOString().split('T')[0];
        const filtered = tasks.filter(task => task.due_date === today && !task.completed);
        ```
      - Or add backend support: modify `/api/tasks` endpoint to accept `?filter=today`
      - Apply filter before rendering task list
  
  - Story: Implement "Overdue" filter to show incomplete tasks with past due dates
    - Acceptance Criteria:
      - Shows only incomplete tasks
      - Shows only tasks with past due dates
      - Completed tasks excluded from view
    - Technical Requirements:
      - Implement client-side filter in `packages/frontend/src/TaskList.js`:
        ```javascript
        const today = new Date().toISOString().split('T')[0];
        const filtered = tasks.filter(task => task.due_date && task.due_date < today && !task.completed);
        ```
      - Or enhance `buildTaskQuery` in `packages/backend/src/app.js` to support overdue filtering
      - Apply filter before rendering task list

- Epic: Data Validation
  - Story: Validate title as required field
    - Acceptance Criteria:
      - Title field cannot be empty
      - Error message shown if title missing
      - Task not created without valid title
    - Technical Requirements:
      - Add `required` prop to title TextField in `packages/frontend/src/TaskForm.js` (already implemented)
      - Frontend validation in `handleSubmit`: `if (!title.trim()) { setError('Title is required'); return; }`
      - Backend validation in `packages/backend/src/app.js` POST/PUT endpoints (already implemented):
        ```javascript
        if (!title || typeof title !== 'string' || title.trim() === '') {
          return res.status(400).json({ error: 'Task title is required' });
        }
        ```
  
  - Story: Handle invalid due dates by treating as absent
    - Acceptance Criteria:
      - Invalid date formats ignored
      - Invalid dates treated as no due date
      - No error thrown for invalid dates
      - Task creation succeeds with invalid date treated as absent
    - Technical Requirements:
      - Add validation in `packages/frontend/src/TaskForm.js` before submission
      - Validate format: `/^\d{4}-\d{2}-\d{2}$/`
      - Backend accepts `null` or empty string for `due_date`
      - Use `due_date || null` in SQL statements in `packages/backend/src/app.js`
      - No 400 error returned for invalid dates
  
  - Story: Validate priority enum values (P1, P2, P3)
    - Acceptance Criteria:
      - Only P1, P2, P3 values accepted
      - Invalid priority values default to P3
      - System maintains data integrity
    - Technical Requirements:
      - Frontend: restrict Select options in `packages/frontend/src/TaskForm.js` to ['P1', 'P2', 'P3']
      - Backend validation in `packages/backend/src/app.js` POST/PUT endpoints:
        ```javascript
        const validPriorities = ['P1', 'P2', 'P3'];
        const priority = validPriorities.includes(req.body.priority) ? req.body.priority : 'P3';
        ```
      - Database constraint via application-layer validation

## Post-MVP Requirements

- Epic: Visual Enhancements
  - Story: Add visual highlighting for overdue tasks
    - Acceptance Criteria:
      - Overdue tasks visually distinct (e.g., red highlighting)
      - Only incomplete overdue tasks highlighted
      - Highlighting applies across all filter views
      - Completed overdue tasks not highlighted
    - Technical Requirements:
      - Update `ListItem` styling in `packages/frontend/src/TaskList.js`
      - Create helper function:
        ```javascript
        const isOverdue = (task) => {
          if (!task.due_date || task.completed) return false;
          return task.due_date < new Date().toISOString().split('T')[0];
        };
        ```
      - Apply conditional styling using Material UI `sx` prop:
        ```javascript
        sx={{ bgcolor: isOverdue(task) ? '#ffebee' : 'transparent', borderLeft: isOverdue(task) ? '4px solid #f44336' : 'none' }}
        ```
  
  - Story: Add color-coded priority badges (Red P1, Orange P2, Gray P3)
    - Acceptance Criteria:
      - P1 tasks display red badge
      - P2 tasks display orange badge
      - P3 tasks display gray badge
      - Badges visible in all views
    - Technical Requirements:
      - Create priority badge in `packages/frontend/src/TaskList.js`
      - Use Material UI `Chip` component with conditional styling:
        ```javascript
        const priorityColors = {
          P1: { bgcolor: '#f44336', color: 'white' }, // red
          P2: { bgcolor: '#ff9800', color: 'white' }, // orange
          P3: { bgcolor: '#9e9e9e', color: 'white' }  // gray
        };
        <Chip label={task.priority} size="small" sx={priorityColors[task.priority]} />
        ```
      - Position next to due date Chip in task list item

- Epic: Task Sorting
  - Story: Implement multi-level sorting logic
    - Acceptance Criteria:
      - Tasks sorted by multiple criteria in defined order
      - Sorting consistent across all views
      - Sorting applied automatically
    - Technical Requirements:
      - Implement sorting function in `packages/frontend/src/TaskList.js` after fetching tasks
      - Chain comparators: overdue → priority → due_date → no date
      - Apply before rendering:
        ```javascript
        const sortedTasks = [...tasks].sort(sortTasksMultiLevel);
        ```
      - Create `sortTasksMultiLevel` function with multiple return conditions
  
  - Story: Sort overdue tasks first
    - Acceptance Criteria:
      - Overdue incomplete tasks appear at top of list
      - Applies as first sorting criteria
      - Only affects incomplete tasks
    - Technical Requirements:
      - First comparator in sort function:
        ```javascript
        const today = new Date().toISOString().split('T')[0];
        const isOverdueA = a.due_date && !a.completed && a.due_date < today;
        const isOverdueB = b.due_date && !b.completed && b.due_date < today;
        if (isOverdueA && !isOverdueB) return -1;
        if (!isOverdueA && isOverdueB) return 1;
        ```
  
  - Story: Sort by priority level (P1, P2, P3)
    - Acceptance Criteria:
      - P1 tasks before P2 tasks
      - P2 tasks before P3 tasks
      - Applied after overdue sorting
    - Technical Requirements:
      - Second comparator in sort function:
        ```javascript
        const priorityMap = { P1: 1, P2: 2, P3: 3 };
        const priorityDiff = priorityMap[a.priority || 'P3'] - priorityMap[b.priority || 'P3'];
        if (priorityDiff !== 0) return priorityDiff;
        ```
  
  - Story: Sort by due date ascending
    - Acceptance Criteria:
      - Earlier due dates appear before later dates
      - Applied after priority sorting
      - Only applies to tasks with due dates
    - Technical Requirements:
      - Third comparator in sort function:
        ```javascript
        if (a.due_date && b.due_date) {
          const dateDiff = a.due_date.localeCompare(b.due_date);
          if (dateDiff !== 0) return dateDiff;
        }
        ```
      - ISO string comparison works for YYYY-MM-DD format
  
  - Story: Display tasks without due dates last
    - Acceptance Criteria:
      - Tasks with no due date appear at bottom
      - Applied after all other sorting criteria
      - Maintains priority order within no-date tasks
    - Technical Requirements:
      - Fourth comparator in sort function:
        ```javascript
        if (a.due_date && !b.due_date) return -1;
        if (!a.due_date && b.due_date) return 1;
        return 0;
        ```
      - Backend SQL in `packages/backend/src/app.js` has: `ORDER BY due_date IS NULL, due_date ASC, created_at ASC`
      - Frontend sorting should align with or override backend ordering
