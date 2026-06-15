---
name: entity-presentation
description: Format and display entities in human-readable format. Use when user says "show me", "display", "summarize", "what is", or when presenting query results. Handles UUID resolution, relationship formatting, and different view modes (summary vs detailed).
author: Nick Silhacek
version: 0.1.0
last_updated: 2026-01-26
---

# Entity Presentation

Format and display entities for human consumption.

## Presentation Modes

### 1. Single Entity View

**Triggers:** "show me [entity]", "display [entity]", "what is [entity]"

```
**[Entity Name]**

| Property | Value |
|----------|-------|
| Type | [type] |
| Status | [status] |
| Created | [date] |

**Relationships**

| Relationship | Entities |
|--------------|----------|
| Part of | [resolved name] |
| Belongs to | [resolved name] |
```

### 2. Entity List View

**Triggers:** Query results, "list [entities]"

**Summary (1-5 results):**
```
**[Query]** (X results)

| Name | Type | Status | Parent |
|------|------|--------|--------|
| [name] | [type] | [status] | [parent] |
```

**Grouped (6+ results):**
```
**[Query]** (X results)

### Tickets (3)
- [name] — [status]

### Tasks (7)
- [name] — [status], part of [parent]
```

### 3. Hierarchy View

**Triggers:** "show hierarchy", "tree view", "structure of"

```
**[Root]** ([type])
├── [Child 1] ([type])
│   ├── [Grandchild 1] ([type])
│   └── [Grandchild 2] ([type])
└── [Child 2] ([type])
```

### 4. Relationship Focus View

**Triggers:** "show what [entity] contains", "who reports to [entity]"

```
**[Entity]** — [relationship]

| Name | Type | Status |
|------|------|--------|
| [related] | [type] | [status] |
```

## UUID Resolution

All UUIDs must resolve to names before display.

**Process:**
1. Load `/_system/index/uuid-map.md` for batch resolution
2. For each UUID, look up path in uuid-map
3. Extract name from the mapped path or load meta.md if needed
4. Replace UUID with name

**Performance:** The uuid-map provides O(1) lookup. For multiple entities, load the map once and resolve all UUIDs in a single pass.

**Unresolved:** Display as `[Unknown: uuid-prefix...]`

## Formatting Rules

**Attributes:**
- Show only non-empty
- Format dates human-readable (e.g., "Jan 21, 2026")

**Relationships:**
- Group by category
- Only show populated relationships
- Resolve all UUIDs

**Empty states:**
- No relationships: "No relationships defined"
- No results: "No [type] found matching criteria"

## View Selection

| Context | View |
|---------|------|
| Single entity | Single Entity View |
| 1-5 results | List (summary) |
| 6+ results | List (grouped) |
| Hierarchy requested | Hierarchy View |
| Specific relationship | Relationship Focus |
