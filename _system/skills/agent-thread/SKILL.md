---
name: agent-thread
description: Close an AI agent session by saving it as a #agent_thread node in Tana. Use when user says "close session", "save this session", "log this thread", or similar session-ending intents.
author: Beverly Hall / Claude
version: 1.2.0
last_updated: 2026-05-07
---

# Agent Thread

An `#agent_thread` node captures what happened in an AI session — what was brought in, what was produced, decisions made, and how to resume it.

---

## Trigger

Use this skill when Beverly says:
- "close session"
- "save this session"
- "log this thread"
- "save this agent thread"

---

## Close Agent Thread Workflow

### Step 1: Gather Fields

Collect the following from the current session:

| Field | What to capture |
|-------|----------------|
| **Title** | `{Matter or topic} | {Session type} | {YYYY-MM-DD}` |
| **Date** | Today's date (YYYY-MM-DD) |
| **Matter** | Client matter(s) or project(s) touched |
| **Tool** | Tool used (Claude Code, Claude.ai, Perplexity, ChatGPT, Other) |
| **Summary** | 1–2 sentences: what this session did |
| **Outputs** | Artifacts produced — files created, nodes created, drafts written, anything you'll need to find again. One item per field instance. |
| **Decisions** | Architectural or strategic choices made — distinct from outputs. One decision per field instance. Omit if none. |
| **File paths** | Local folder or file paths touched. One path per field instance. Omit if none. |
| **Re-entry prompt** | A distilled prompt Beverly can paste at the start of a new Claude Code session to resume this thread. Include: goal, key context, where you left off, and the most important file path or node ID. |
| **Comes from: Effort** | The project or matter node this thread served. Set to the Focus Stream node ID when the session touched a specific matter. Omit for system/design sessions with no project. |
| **Status** | `active` if thread may continue; `archived` if complete |

### Step 1b: Ask About Project Link

Before building the paste, ask:

> "Which project should this thread be linked to, if any?"

If Beverly names a project, look up its node ID from the reference memory or search Tana. Use that ID for the `Comes from: Effort` field (`662D2ZohH5oH`) in the paste.

If Beverly says "none" or similar, omit the `662D2ZohH5oH` line from the paste.

**Known project node IDs:**

| Project | Node ID |
|---------|---------|
| Decision Threads (fs) | `f5M2ZK4blHOi` |
| Practice Intelligence (fs) | `8LoMtLPbAKIF` |
| Personal Intelligence (fs) | `A18qqn4raLOC` |
| Mountain Legacy VC (fs) | `-KxeRkxFmRdu` |
| Prien Lake AH (fs) | see reference_tana memory |

For any project not listed, use `search_nodes` to find its node ID.

### Step 2: Post to Today's Day Page

Always post the `#agent_thread` node as a child of today's Day page in Tana — never directly to a project node (see decision trace 2026-05-03: Tana Routing — Day Page as Parent Rule).

Get today's node:
```
get_or_create_calendar_node(workspaceId: "kUwWrA1V3x", granularity: "day")
```

Note the returned `nodeId` — this is the parent for the paste.

### Step 3: Build and Import the Tana Paste

Use `import_tana_paste` with the following format. All field references MUST use `[[^fieldID]]::` syntax — bare field IDs without the `[[^...]]` wrapper create orphan plain-text children instead of setting fields. Repeat multi-value lines for each item.

```
%%tana%%
- {Title} #[[^fCjrPBlCsqXv]]
  - [[^vQQGCbO_pX3K]]:: {YYYY-MM-DD}
  - [[^z8DNVKcCjA3K]]:: {Matter}
  - [[^4pIVUEB4m12j]]:: [[^{tool node ID}]]
  - [[^r824jhqAWozG]]:: {Summary}
  - [[^HyPfQno3oJRp]]:: {Output 1}
  - [[^HyPfQno3oJRp]]:: {Output 2}
  - [[^beX-AoQpa_rN]]:: {Decision 1}
  - [[^beX-AoQpa_rN]]:: {Decision 2}
  - [[^eNFS-JJiJ9dB]]:: {File path 1}
  - [[^eNFS-JJiJ9dB]]:: {File path 2}
  - [[^G8p1LiotSh3v]]:: {Re-entry prompt}
  - [[^662D2ZohH5oH]]:: [[^{project node ID}]]
```

**Omit** the `[[^662D2ZohH5oH]]` line for system/design sessions with no specific project.

**Do NOT** add Status or Vault lines — the tag applies `active` and `Resources Vault` as defaults automatically. Only add Status explicitly when archiving:
```
  - [[^CAXcNBcUA4JG]]:: [[^klq0-6Ue3Ew1]]
```

**Tool option node IDs:**

| Tool | Node ID |
|------|---------|
| Claude Code | `tV_q9Qzecn41` |
| Claude.ai | `Y0oFbP0Dvk-W` |
| Perplexity | `0iOousrVWEjw` |
| ChatGPT | `CdrEmvZwrsv0` |
| Other | `kHDBaomUrLoD` |

**Status option node IDs (only needed when archiving):**

| Status | Node ID |
|--------|---------|
| archived | `klq0-6Ue3Ew1` |

Call:
```
import_tana_paste(workspaceId: "kUwWrA1V3x", content: "...", parentNodeId: {today_node_id})
```

### Step 4: Confirm

Tell Beverly the node was saved and confirm the title so she can find it in the Agent Threads log (`ps2ZsPggalmw`).

---

## Field ID Reference

| Field | ID | Notes |
|-------|----|-------|
| `#agent thread` tag | `fCjrPBlCsqXv` | |
| Date | `vQQGCbO_pX3K` | |
| Matter | `z8DNVKcCjA3K` | |
| Tool | `4pIVUEB4m12j` | Options field |
| Summary | `r824jhqAWozG` | |
| Outputs | `HyPfQno3oJRp` | Multi-value |
| Decisions | `beX-AoQpa_rN` | Multi-value; added 2026-05-07 |
| File paths | `eNFS-JJiJ9dB` | Multi-value |
| Re-entry prompt | `G8p1LiotSh3v` | Added 2026-05-07 |
| Status | `CAXcNBcUA4JG` | Options: active / archived |
| Comes from: Effort | `662D2ZohH5oH` | Inherited from #Resources family; set to project node ID for matter-specific sessions |
| Vault | `JsF0fCB2YoVs` | Default: Resources Vault (`dz_cS6eYHrc7`) |

---

## Tana Reference

| Item | Value |
|------|-------|
| Workspace ID | `kUwWrA1V3x` |
| Agent Threads log node | `ps2ZsPggalmw` |
| Resources Vault | `dz_cS6eYHrc7` |
