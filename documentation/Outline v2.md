# Chrono — Time Management App

## Project Outline

---

## Abstract

This time management software tracks time spent on projects and provides analytics to help understand how time is used. The finished product should support reporting time for freelance projects, estimating how long future projects will take, making better use of time day-to-day, and staying focused on tasks.

The style should be casual and low-friction, requiring very little setup to get started. There should be few limitations on how a user chooses to use the software, and the learning curve should be nearly zero.

---

## Tech Stack

| Layer           | Technology                           |
| --------------- | ------------------------------------ |
| Frontend        | SvelteKit + TypeScript + TailwindCSS |
| Desktop Runtime | Tauri 2.x                            |
| Backend Logic   | Rust                                 |
| Database        | PostgreSQL 16 via sqlx               |
| Build Tool      | Vite                                 |
| IDE             | WebStorm                             |

---

## Data Model

### `projects`

|Column|Type|Notes|
|---|---|---|
|`id`|`UUID`|Primary key|
|`name`|`TEXT NOT NULL`|Display name|
|`subtext`|`TEXT`|Optional short description|
|`color`|`TEXT`|Optional hex color for UI accents|
|`is_favorite`|`BOOL DEFAULT false`||
|`is_archived`|`BOOL DEFAULT false`||
|`archived_at`|`TIMESTAMPTZ`|Set when archived|
|`created_at`|`TIMESTAMPTZ DEFAULT now()`||
|`last_opened_at`|`TIMESTAMPTZ`|Updated on project page open|

### `work_sessions`

|Column|Type|Notes|
|---|---|---|
|`id`|`UUID`|Primary key|
|`project_id`|`UUID REFERENCES projects`||
|`started_at`|`TIMESTAMPTZ NOT NULL`||
|`ended_at`|`TIMESTAMPTZ`|NULL means session is active|
|`duration_seconds`|`INT`|Computed and stored on session end|

### `notes`

|Column|Type|Notes|
|---|---|---|
|`id`|`UUID`|Primary key|
|`project_id`|`UUID REFERENCES projects`||
|`session_id`|`UUID REFERENCES work_sessions`|Optional — links note to a session|
|`body`|`TEXT NOT NULL`|The note content|
|`tags`|`TEXT[]`|Array of tag strings, e.g. `["design", "review"]`|
|`created_at`|`TIMESTAMPTZ DEFAULT now()`||
|`updated_at`|`TIMESTAMPTZ`|Set on edit|

### `tags` _(derived, not a table)_

Tags are stored as a `TEXT[]` column on `notes`. A distinct tag list is derived via query. No normalization needed for MVP — tags are freeform strings.

### `planned_items`

|Column|Type|Notes|
|---|---|---|
|`id`|`UUID`|Primary key|
|`project_id`|`UUID REFERENCES projects`|Optional|
|`title`|`TEXT`|Optional|
|`description`|`TEXT`|Optional|
|`planned_date`|`DATE`|Optional — null sends item to undated list|
|`planned_time_start`|`TIME`|Optional|
|`planned_time_end`|`TIME`|Optional|
|`repeat_rule`|`TEXT`|Optional — e.g. `"weekly"`, `"weekdays"`, iCal RRULE|
|`created_at`|`TIMESTAMPTZ DEFAULT now()`||
|_Constraint_|At least one of `project_id`, `title`, or `description` must be non-null (enforced in app logic)||

### `task_quadrants`

|Column|Type|Notes|
|---|---|---|
|`id`|`UUID`|Primary key|
|`position`|`INT`|0–3, maps to the four quadrant positions|
|`title`|`TEXT`|User-defined label, e.g. "Work", "Personal"|

### `task_items`

|Column|Type|Notes|
|---|---|---|
|`id`|`UUID`|Primary key|
|`quadrant_id`|`UUID REFERENCES task_quadrants`||
|`body`|`TEXT NOT NULL`||
|`is_completed`|`BOOL DEFAULT false`|Shown as strikethrough|
|`position`|`INT`|For ordering within quadrant|
|`created_at`|`TIMESTAMPTZ DEFAULT now()`||

---

## Migrations Plan

All migrations live in `migrations/` and are run with `sqlx migrate run`.

|File|Purpose|
|---|---|
|`0001_create_projects.sql`|`projects` table|
|`0002_create_work_sessions.sql`|`work_sessions` table|
|`0003_create_notes.sql`|`notes` table with `TEXT[]` tags|
|`0004_create_planned_items.sql`|`planned_items` table|
|`0005_create_task_quadrants.sql`|`task_quadrants` + `task_items` tables|

---

## Feature Modules

### Module 1 — Core Project Management

_Manages the list of projects and their metadata._

**Features:**

- Display all projects in a list view
- Each project card shows: name, subtext, color accent, favorite status, created date, last opened date
- Create a new project (name required, subtext + color optional)
- Open a project's detail page (updates `last_opened_at`)
- Project settings panel: rename, edit subtext/color, favorite/unfavorite, archive/unarchive, delete
- Sections: Favorites (expanded by default) → Normal (expanded) → Archived (collapsed)
- Sort controls: Alphabetical, Created Date, Last Opened (applied within each section)
- Archived section has an independent sort: Time Archived

**Data interactions:** `projects` table — full CRUD

---

### Module 2 — Time Tracking

_Start/stop work sessions tied to projects._

**Features:**

- "Start Work" button on a project page begins a session (`work_sessions` row, `ended_at = NULL`)
- Only one active session at a time — starting a new project auto-stops the previous one
- Active project replaces "Start Work" with "Stop Work"
- Active project is visually indicated on the project list (e.g. pulsing dot)
- Session duration displayed live while active
- Session history visible on the project page: list of past sessions with date, start time, end time, duration

**Data interactions:** `work_sessions` — insert on start, update `ended_at` + `duration_seconds` on stop

---

### Module 3 — Notes & Tags

_Freeform notes attached to projects and optionally to sessions._

**Features:**

- Notes section on each project page
- Add a note at any time (not limited to active sessions)
- Notes display: body text, timestamp, tags
- Inline tag entry — type `#tagname` or use a tag picker
- Edit any note after the fact (updates `updated_at`)
- Delete a note
- Filter notes by tag within a project's notes view
- Tag autocomplete pulls from previously used tags across all projects

**Data interactions:** `notes` table — full CRUD; tag list derived via `SELECT DISTINCT unnest(tags)`

---

### Module 4 — Timetable / Calendar Views

_Visual representation of time worked across day/week/month._

#### 4a — Monthly View

- Grid of all days in a selected month
- Each day cell lists the names of projects worked on that day
- Click a day to jump to Daily View for that date

#### 4b — Weekly View

- 7-column layout (one per day)
- Each project worked shows as a colored time block within the day column
- Blocks sized and positioned by actual start/end times
- Overlapping sessions handled gracefully (side-by-side or layered)

#### 4c — Daily View

- Chronological list of all sessions for the selected day
- Each session is expandable to show its attached notes
- Sessions show project name, time range, and duration

#### 4d — Planning Mode (overlay on calendar views)

- Toggle between "Logged" and "Planning" modes
- In Planning mode, create planned items on any day/time slot
- Required: at least one of project, title, or description
- Optional: day, time range, repeat rule
- Undated items appear in a sidebar "Unscheduled" list
- Planned items visually distinct from logged sessions (dashed border, lighter color)
- Planned items can be dragged to reschedule

**Data interactions:** `work_sessions` (read), `planned_items` (full CRUD)

---

### Module 5 — Task Quadrant List

_A detached, visual notebook-style task list. No connection to projects or time tracking._

**Features:**

- Styled to look like a piece of lined notebook paper folded into 4 quadrants
- Each quadrant has an editable underlined title
- Each quadrant holds a list of task items
- Items can be crossed off (strikethrough) when completed; crossed-off items remain visible
- Items can be deleted
- Each empty quadrant shows greyed-out example tasks randomly drawn from themes: Work, Personal, Health, School, Social, Chores
- Each quadrant has a circled ⓘ button with tooltip: _"Use the 4 quadrants to divide and record everything that you want to do. This is intended to help you remember what you intend to accomplish. Try to keep each item as simple as possible..."_
- Quadrant titles and item ordering persist across sessions

**Data interactions:** `task_quadrants`, `task_items` — full CRUD

---

## Milestones

### Milestone 1 — Project List + Time Tracking (MVP Core)

_Goal: A working app you can actually use to track time on your graphics projects._

**Deliverables:**

1. PostgreSQL connection via sqlx — connection pool in Rust, config from environment or `.env`
2. Migrations 0001 and 0002 run successfully
3. Tauri commands: `get_projects`, `create_project`, `update_project`, `delete_project`, `get_project_by_id`
4. Tauri commands: `start_session`, `stop_session`, `get_active_session`, `get_sessions_for_project`
5. Homepage (`/`) — project list with sections (Favorites, Normal, Archived) and sort controls
6. Project page (`/project/[id]`) — shows project details, Start/Stop button, session history
7. New project modal or inline form
8. Project settings panel (rename, subtext, favorite, archive, delete)
9. Live session timer displayed while a session is active
10. Active project indicator on the project list

**Definition of done:** You can create projects, start/stop work sessions, and see how long you've worked.

---

### Milestone 2 — Notes & Tags

_Goal: Attach context to your work sessions so you remember what you were doing._

**Deliverables:**

1. Migration 0003
2. Tauri commands: `create_note`, `update_note`, `delete_note`, `get_notes_for_project`, `get_all_tags`
3. Notes section on the project page — chronological list, add/edit/delete
4. Tag input — type inline or pick from autocomplete dropdown
5. Filter notes by tag (tag pills as filter toggles above the notes list)
6. Notes linked to sessions when created while a session is active

**Definition of done:** You can leave timestamped, tagged notes on any project and filter them to understand your work patterns.

---

### Milestone 3 — Timetable Views

_Goal: See how you've actually been spending your time._

**Deliverables:**

1. Timetable page (`/timetable`) with view toggle: Day / Week / Month
2. Monthly view — day grid with project name chips
3. Weekly view — time block grid with colored project blocks
4. Daily view — session list with expandable notes
5. Navigation: previous/next period, jump to today
6. Migration 0004
7. Planning mode toggle
8. Create/edit/delete planned items on the calendar
9. Undated items sidebar

**Definition of done:** You can review any past week or month, see where your time went, and plan upcoming work blocks.

---

### Milestone 4 — Task Quadrant List

_Goal: A lightweight, detached to-do list for capturing everything you need to do._

**Deliverables:**

1. Migrations 0005
2. Tauri commands: `get_quadrants`, `update_quadrant_title`, `get_tasks_for_quadrant`, `create_task`, `update_task`, `delete_task`
3. Task list page (`/tasks`) with notebook paper styling
4. 4-quadrant layout — editable titles, task item input
5. Cross-off interaction (strikethrough), delete items
6. Empty state: greyed-out themed suggestions, ⓘ tooltip
7. Persistent quadrant titles and item order

**Definition of done:** You have a persistent, styled scratchpad for capturing tasks across all areas of life, completely independent of the time tracker.

---

## File Structure (Planned)

```
project-root/
├── src/                          # SvelteKit frontend
│   ├── lib/
│   │   ├── components/
│   │   │   ├── projects/         # ProjectCard, ProjectList, ProjectSettings
│   │   │   ├── sessions/         # SessionTimer, SessionHistory
│   │   │   ├── notes/            # NoteItem, NoteEditor, TagInput, TagFilter
│   │   │   ├── timetable/        # MonthView, WeekView, DayView, PlanItem
│   │   │   └── tasks/            # Quadrant, TaskItem, NotebookLayout
│   │   ├── stores/               # Svelte stores: activeSession, projectList, etc.
│   │   ├── api/                  # Typed wrappers around Tauri invoke() calls
│   │   └── types.ts              # Shared TypeScript types mirroring DB models
│   └── routes/
│       ├── +page.svelte          # Homepage — project list
│       ├── project/[id]/
│       │   └── +page.svelte      # Project detail page
│       ├── timetable/
│       │   └── +page.svelte      # Timetable views
│       └── tasks/
│           └── +page.svelte      # Task quadrant page
├── src-tauri/
│   ├── src/
│   │   ├── main.rs
│   │   ├── db.rs                 # sqlx connection pool setup
│   │   ├── models/               # Rust structs matching DB tables
│   │   │   ├── project.rs
│   │   │   ├── session.rs
│   │   │   ├── note.rs
│   │   │   ├── planned_item.rs
│   │   │   └── task.rs
│   │   └── commands/             # Tauri #[tauri::command] functions
│   │       ├── projects.rs
│   │       ├── sessions.rs
│   │       ├── notes.rs
│   │       ├── planned_items.rs
│   │       └── tasks.rs
│   ├── Cargo.toml
│   └── tauri.conf.json
├── migrations/
│   ├── 0001_create_projects.sql
│   ├── 0002_create_work_sessions.sql
│   ├── 0003_create_notes.sql
│   ├── 0004_create_planned_items.sql
│   └── 0005_create_task_quadrants.sql
└── .env                          # DATABASE_URL and other config
```

---

## Open Questions / Future Considerations

- **Analytics page** — charts for time-per-project over a date range, tag frequency breakdown, average session length
- **Export** — CSV or PDF time reports for freelance invoicing
- **Search** — full-text search across notes
- **Multiple windows** — floating timer window while working in another app
- **Repeat rules** — how complex should `repeat_rule` be? Simple strings (`"weekly"`) vs full iCal RRULE parsing
- **Tag normalization** — keep freeform `TEXT[]` or normalize into a `tags` table in a later migration?