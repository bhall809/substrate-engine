---
name: task-performance
description: Capture daily task plans from Tana Status Boards and record completions at end of day. Compute performance metrics over time. Use when user says "morning check-in", "end of day review", "how am I doing on tasks", "task performance summary", "task completion rate", or similar performance-tracking intents.
author: Beverly Hall / Claude
version: 1.2.0
last_updated: 2026-05-07
---

# Task Performance

Track planned vs. completed tasks each day to assess performance over time. Each day produces one `daily-log` entity — created in the morning from the Tana Status Board, updated in the evening with completed tasks.

---

## AI Administrator Integration

**Important:** This skill handles Substrate-side logging only (daily-log entities, Status Board updates, completion metrics). Beverly also runs corresponding workflows in the AI Administrator system at `/Users/beverlylaw/Dropbox/AI Administrator Starter Kit—Reclaim Your Creative Mind/AI Administrator/`.

When running morning or evening check-ins, always offer to run or confirm the AI Administrator counterpart:

| Substrate workflow | AI Administrator counterpart |
|-------------------|------------------------------|
| Morning Workflow | `Saved Actions/Run Morning Check-In.md` (Clarity Lenses + Wisdom Audit) |
| Evening Workflow | `Saved Actions/Run Evening Check-In.md` (Principle Reflection + Hard Thing Check + Activity Audit + Attractor Pass + Flow Block Selection) |
| On-demand (stuck/slow) | `Saved Actions/Run Wisdom Audit.md` |

Read the AI Administrator ROUTING file before executing any of these: `ROUTING (AI always read).md`.

---

## Triggers

| User says… | Action |
|------------|--------|
| "morning check-in" | Run Morning Workflow |
| "end of day review" / "end of day" | Run Evening Workflow |
| "task performance summary" / "how am I doing on tasks" / "show me my completion trends" | Run Summary Report |
| "log today's tasks" / "capture today's plan" | Run Morning Workflow |

---

## Morning Workflow — Status Board Capture

### Step 1: Get Today's Daily Note in Tana

```
get_or_create_calendar_node(workspaceId: "kUwWrA1V3x", granularity: "day")
```

This returns the Today Page node (tagged `#DNP`). Note its `nodeId`.

### Step 2: Find the Status Board

Read the Today Page's children:

```
get_children(nodeId: {today_node_id})
```

Look for a child node whose name contains "Status Board" or that is tagged with a Status Board tag. If multiple candidates exist, present them to Beverly and ask which one to use.

If no Status Board is found:
- Tell Beverly: "I didn't find a Status Board on today's page. Would you like to capture tasks another way, or skip today?"
- Do not create the daily-log entity until a plan source is confirmed.

Note the Status Board's `nodeId` — save as `tana_board_node`.

### Step 3: Extract Planned Tasks

```
read_node(nodeId: {status_board_node_id}, maxDepth: 2)
```

Parse the markdown output:
- Collect all task-like items (lines starting with `- [ ]`, `- [x]`, or similar checkbox syntax)
- Include both checked and unchecked items — the full board represents what was planned
- Count total items → `planned_count`
- Format as a markdown list → `planned_tasks`

### Step 4: Create the Daily Log Entity

Follow the entity-management skill to create a `daily-log` entity with:

```yaml
name: Daily Log — {YYYY-MM-DD}
type: daily-log
description: Task performance log for {date}. {planned_count} tasks planned from Status Board.
status: open
date: {YYYY-MM-DD}
planned_count: {N}
planned_tasks: |
  {markdown list from Step 3}
tana_board_node: {nodeId from Step 2}
meta_status: live
created: {ISO datetime}
modified: {ISO datetime}
```

Register in the appropriate shard index (`/entities/daily-log/_index-{shard}.md`) using the first character of the UUID to select the shard:
- 0–3 → `_index-0-3.md`
- 4–7 → `_index-4-7.md`
- 8–b → `_index-8-b.md`
- c–f → `_index-c-f.md`

Shard index row format:
```
| {uuid} | {name} | {date} | open | — | {created} |
```

### Step 5: Present the Plan

Show Beverly a clean summary:

```
Today's Plan — {date}
{planned_count} tasks from Status Board

{numbered list of tasks}

Daily log created. I'll record completions when you do your end-of-day review.
```

---

## Evening Workflow — Completed Tasks Review

### Step 1: Locate Today's Daily Log Entity

Search the shard indexes for a `daily-log` entity with today's date:
- Read all four `_index-*.md` files under `/entities/daily-log/`
- Find the row where `Date` matches today's ISO date
- Load that entity's `meta.md`

If no entity exists for today (morning workflow wasn't run):
- Offer to run the Morning Workflow first, or ask Beverly to provide the planned task count manually so the evening review can still be recorded.

### Step 2: Pull Completed Tasks from Tana

Get today's calendar node:
```
get_or_create_calendar_node(workspaceId: "kUwWrA1V3x", granularity: "day")
```

If `tana_board_node` is stored in the entity, re-read the Status Board to see current checkbox states:
```
read_node(nodeId: {tana_board_node}, maxDepth: 2)
```

Alternatively, read today's full Daily Note at `maxDepth: 3` to catch completions recorded anywhere on the page.

Parse output:
- Completed items: lines with `- [x]` or equivalent checked syntax
- `completed_tasks` = formatted markdown list of those items
- `completed_count` = count of completed items

**Note on date query workaround:** If querying tasks across the workspace by due date, read project nodes at `maxDepth: 2` and scan for date strings in task names (see Tana reference memory). For end-of-day review, reading the Status Board node directly is preferred and avoids this issue.

### Step 3: Compute Metrics

```
completion_rate = round((completed_count / planned_count) × 100)
```

If `planned_count` is 0, set `completion_rate` to null and note that no plan was captured.

### Step 4: Update the Entity

Edit the `daily-log` entity's `meta.md`:

```yaml
status: reviewed
completed_count: {N}
completion_rate: {0-100}
completed_tasks: |
  {markdown list}
principle_active: {principle named in evening check-in, if provided}
principle_reflection: {one line on how it played out, if provided}
attractor_connection: {attractor name and brief note, if surfaced}
modified: {ISO datetime}
```

`principle_active`, `principle_reflection`, and `attractor_connection` are optional — only populate if Beverly ran an Evening Check-In and these surfaced. If the Evening Check-In was not run, leave these fields absent.

Update the shard index row to reflect the new status and completion rate.

### Step 5: Present the Review

```
End-of-Day Review — {date}
Planned: {planned_count} tasks
Completed: {completed_count} tasks
Completion rate: {completion_rate}%

Completed:
{list}

Not completed:
{list of items from planned_tasks not in completed_tasks}
```

Add a brief observation if a pattern is noticeable (e.g., if this is the third day below 50%). Keep it factual — do not coach unless Beverly asks.

### Step 6: Save Status Board to Substrate

After presenting the review, update the active Status Board document in Substrate to reflect end-of-day state.

**Location:** `entities/document/a1/d3/a1d3b746-68a4-45f6-a252-247bfa8a48f5/status-board.md`

Update the file with:
- The full current Status Board (all categories and tasks)
- All final statuses from today
- Uncompleted tasks carried forward (marked ⬜ Not Started or 🔄 In Progress)
- Waiting items preserved in the Waiting section
- The date updated to tomorrow's date at the top

Also update the `modified` timestamp in the entity's `meta.md`.

**Retrieval phrase for new sessions:** Beverly says *"Load my active Status Board from Substrate"* and the agent reads this file and displays it.

This step runs automatically at end of day — no need to ask Beverly for permission.

### Step 7: Offer Agent Thread Close

After the Status Board save, ask:

> "Want to close this session as an agent thread in Tana?"

If yes, read the `agent-thread` skill (`_system/skills/agent-thread/SKILL.md`) and run the Close Agent Thread workflow. The Summary, Outputs, Decisions, File paths, Re-entry prompt, and Status fields should all reflect the full session — not just the check-in portion.

If no, end the evening workflow normally.

---

## Summary Report — On-Demand Performance Review

### Step 1: Load Daily Log Entities

Read all four shard indexes:
```
/entities/daily-log/_index-0-3.md
/entities/daily-log/_index-4-7.md
/entities/daily-log/_index-8-b.md
/entities/daily-log/_index-c-f.md
```

Collect all entity rows. For each entity with `status: reviewed`, load its `meta.md` to get `date`, `planned_count`, `completed_count`, `completion_rate`.

Apply any date filter Beverly specifies ("last 7 days", "this week", "March"). Default to the last 30 days if no filter given.

### Step 2: Compute Aggregate Statistics

| Metric | How to compute |
|--------|----------------|
| Average completion rate | Mean of `completion_rate` across reviewed days |
| Best day | Highest `completion_rate` |
| Worst day | Lowest `completion_rate` |
| Days fully complete | Count where `completion_rate` = 100 |
| Days below 50% | Count where `completion_rate` < 50 |
| Day-of-week pattern | Group by weekday, average rate per weekday |
| Trend | Compare first-half average vs. second-half average of the period |

### Step 3: Present the Report

Format as a readable summary:

```
Task Performance — {period}
{N} days reviewed

Average completion rate: {X}%
Best day: {date} ({X}%)
Worst day: {date} ({X}%)
Fully complete days: {N}
Days below 50%: {N}

By day of week:
  Monday: {X}%
  Tuesday: {X}%
  ...

Trend: {Improving / Declining / Stable} (first half avg {X}% → second half avg {X}%)
```

If fewer than 5 reviewed days exist, note that the sample is too small for reliable trends and show what's available.

---

## Edge Cases

| Situation | Handling |
|-----------|----------|
| No Status Board found on Today Page | Ask Beverly to confirm plan source or skip |
| Morning workflow already run today | Load the existing entity instead of creating a duplicate; confirm with Beverly before overwriting |
| Evening review before morning capture | Offer to create a minimal entity with `planned_count` provided manually |
| `planned_count` is 0 | Set `completion_rate` to null; note in the entity |
| Tana MCP unavailable | Ask Beverly to provide the task list manually; proceed with entity creation from manual input |
| Day-of-week pattern shows a consistent low day | Surface the observation in the summary; do not interpret it unless Beverly asks |

---

## Tana Reference

| Item | Value |
|------|-------|
| Workspace ID | `kUwWrA1V3x` |
| Get today's node | `get_or_create_calendar_node(workspaceId: "kUwWrA1V3x", granularity: "day")` |
| Daily Note tag | `#DNP` |
| Date query workaround | Read project nodes at `maxDepth: 2`, scan task names for date strings (until Beverly fully transitions to absolute dates) |

---

## Entity Reference

- **Type:** `daily-log`
- **Entity folder:** `/entities/daily-log/`
- **Shard indexes:** `/entities/daily-log/_index-{0-3,4-7,8-b,c-f}.md`
- **Attributes:** See `/_system/schema/attributes.md` → `## daily-log`
- **Grouping:** `log`
