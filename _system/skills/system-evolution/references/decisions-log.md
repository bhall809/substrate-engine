# Architecture Decisions Log

This document records significant architectural decisions for Substrate, including context and rationale.

## Table of Contents

- [2026-01-25: UUID-Prefix Sharding for Entity Storage](#2026-01-25-uuid-prefix-sharding-for-entity-storage)
- [2026-01-25: Global UUID Map Index](#2026-01-25-global-uuid-map-index)
- [2026-01-25: Adjacency Index for Relationships](#2026-01-25-adjacency-index-for-relationships)
- [2026-01-25: Sharded Type Indexes](#2026-01-25-sharded-type-indexes)
- [2026-01-25: Pending Updates Queue](#2026-01-25-pending-updates-queue)
- [2026-01-25: Folder Renamed from "vaults" to "entities"](#2026-01-25-folder-renamed-from-vaults-to-entities)
- [2026-01-25: Dot-Prefix for System Folders](#2026-01-25-dot-prefix-for-system-folders)
- [2026-01-25: Property Constraints Model](#2026-01-25-property-constraints-model)
- [2026-01-25: Decision Trace Logging](#2026-01-25-decision-trace-logging)
- [2026-01-25: Index Operations Abstraction Layer](#2026-01-25-index-operations-abstraction-layer)
- [2026-01-25: Renamed fsp-schema-migration to schema-evolution](#2026-01-25-renamed-fsp-schema-migration-to-schema-evolution)
- [2026-02-03: Clean Workspace Root Rule](#2026-02-03-clean-workspace-root-rule)
- [2026-02-03: UUID-Map Path Format Clarification](#2026-02-03-uuid-map-path-format-clarification)
- [2026-02-03: Remove blocks/blocked-by Relationship](#2026-02-03-remove-blocksblocked-by-relationship)

---

## 2026-01-25: UUID-Prefix Sharding for Entity Storage

**Decision:** Store entities in sharded folders using UUID prefix: `/entities/{type}/{prefix-2}/{prefix-4}/{uuid}/`

**Context:** Needed to support systems with 100,000-500,000 entities without file system performance degradation.

**Rationale:** Most file systems slow down when directories contain thousands of entries. Two-level sharding (2 chars + 2 chars) creates 65,536 possible paths, keeping each directory under ~256 items even at scale.

**Trade-offs:**
- Paths are longer and less human-readable
- Entity lookup requires knowing the UUID (can't browse by name in file system)
- Mitigated by uuid-map index for O(1) lookup

---

## 2026-01-25: Global UUID Map Index

**Decision:** Maintain `/_system/index/uuid-map.md` mapping every UUID to its path.

**Context:** With sharded storage, finding an entity by UUID would require scanning all shards without an index.

**Rationale:** Provides O(1) lookup from UUID to entity path. Single file to load, simple format.

**Trade-offs:**
- Another file to maintain
- Can grow large (estimated ~50MB at 500k entities)
- Must stay in sync with actual entities
- Future consideration: migrate to SQLite at millions of entities

---

## 2026-01-25: Adjacency Index for Relationships

**Decision:** Maintain `/_system/index/adjacency.md` with pre-computed relationship graph.

**Context:** Relationship traversal (e.g., "everything under Campaign X") was slow because it required loading each entity's meta.md.

**Rationale:** Pre-computing relationships allows traversal without touching entity files. Entire graph in one file.

**Trade-offs:**
- Duplicates data from meta.md files
- Must stay in sync when relationships change
- Can grow large with dense relationships
- meta.md remains source of truth; adjacency can be rebuilt

---

## 2026-01-25: Sharded Type Indexes

**Decision:** Split type indexes into four shards by UUID first character: `_index-0-3.md`, `_index-4-7.md`, `_index-8-b.md`, `_index-c-f.md`

**Context:** A single `_index.md` with 100k entries would be slow to parse and update.

**Rationale:** Four shards keep each file to ~25k entries max. Parallel processing possible.

**Trade-offs:**
- More files to manage
- Listing all entities of a type requires reading four files
- Shard assignment is deterministic (UUID-based), so updates hit correct shard

---

## 2026-01-25: Pending Updates Queue

**Decision:** Add `/_system/index/pending.md` to queue index updates for batch processing.

**Context:** Real-time index updates on every operation could become a bottleneck.

**Rationale:** Allows fast writes (just queue the change) with periodic batch processing. Also provides recovery if interrupted.

**Trade-offs:**
- Indexes may be temporarily stale
- Requires processing step (manual or scheduled)
- Query results may not reflect very recent changes until processed

---

## 2026-01-25: Folder Renamed from "vaults" to "entities"

**Decision:** Use `entities/` as the folder name, while "vault" remains acceptable in prose.

**Context:** "entities" is more immediately descriptive for agents and new users.

**Rationale:** Folder name should be self-documenting. "vault" has nice metaphorical weight but isn't as clear. Compromise: folder is `entities/`, but documentation can refer to "the task vault."

**Trade-offs:**
- Slight terminology inconsistency (folder vs prose)
- Acceptable because folder name is what agents see, prose is for humans

---

## 2026-01-25: Dot-Prefix for System Folders

**Decision:** Use `_system/` and `_system/skills/` (hidden folders) instead of `_system/`.

**Context:** Needed consistency between system folder and skills folder naming.

**Rationale:** `_system/skills/` appears to be a Cowork convention. Matching it with `_system/` provides consistency. Both are "infrastructure" folders that users don't need to see in normal file browsing.

**Trade-offs:**
- Hidden by default in most file browsers
- Users must toggle hidden files to see them
- Acceptable because these are agent-facing, not user-facing

---

## 2026-01-25: Property Constraints Model

**Decision:** Types can have Required, Forbidden, and Allowed (default) properties, defined in `types.md`.

**Context:** Needed a way to enforce that certain properties must exist on certain types, or must not exist.

**Rationale:** Three-tier model covers all cases:
- Required: must exist, must have value
- Forbidden: not allowed on this type
- Allowed (default): optional

Constraints live in `types.md` alongside the type definitions, so one lookup gives you everything about a type.

**Trade-offs:**
- Starting with all properties Allowed (no constraints) for flexibility
- Can tighten later as patterns emerge

---

## 2026-01-25: Decision Trace Logging

**Decision:** Log agent decisions in `/_system/logs/traces/` with structured format including context, rules applied, precedents, and exceptions.

**Context:** Inspired by "context graph" concept - capturing not just what happened but why.

**Rationale:** Creates queryable precedent for future decisions. Enables auditing. Supports learning from past exceptions.

**Trade-offs:**
- Additional overhead per operation
- Log files can grow large (mitigated by archiving)
- Value depends on actually querying/using the traces

---

## 2026-01-25: Index Operations Abstraction Layer

**Decision:** Define abstract operations (resolve_uuid, get_relationships, etc.) in `index-operations.md` rather than having skills reference specific file formats.

**Context:** Planning for future migration from markdown indexes to SQLite.

**Rationale:** Skills describe *what* to do; the reference doc describes *how*. When backend changes, only the reference doc needs updating.

**Trade-offs:**
- Extra indirection
- Skills must reference the operations doc
- Worth it for future-proofing

---

## 2026-01-25: Renamed fsp-schema-migration to schema-evolution

**Decision:** Rename the skill from `fsp-schema-migration` to `schema-evolution`.

**Context:** The term "migration" implies a one-time move from state A to state B. The skill actually handles ongoing schema changes as the system grows.

**Rationale:** "Evolution" better captures the continuous, iterative nature of schema changes. It aligns with the philosophy that systems grow and adapt over time rather than being migrated between fixed states.

**Trade-offs:**
- Slightly longer name
- Matches the meta-skill naming (system-evolution)
- Better semantic fit for the actual operations performed

---

## Template for New Decisions

```
## YYYY-MM-DD: Decision Title

**Decision:** What was decided

**Context:** Why this decision was needed

**Rationale:** Why this approach was chosen

**Trade-offs:**
- Pro/con 1
- Pro/con 2
```

---

## 2026-02-03: Clean Workspace Root Rule

**Decision:** Only system folders and CLAUDE.md belong at workspace root. All other files go to staging first.

**Context:** Stray files (HTML, ICS) were found at workspace root, violating the expected structure.

**Rationale:** The workspace root should be predictable and clean. If a file isn't system infrastructure, it either:
- Belongs in `staging/` (to be processed into an entity)
- Already is an entity (in `entities/`)

This makes the workspace self-documenting and prevents clutter.

**Trade-offs:**
- Users must route files through staging (slight friction)
- Predictable structure aids agent navigation and validation

---

## 2026-02-03: UUID-Map Path Format Clarification

**Decision:** Paths in uuid-map.md are relative to `entities/` folder, not workspace root.

**Context:** Agent error caused inconsistent uuid-map entries — some paths had `entities/` prefix, some didn't. This broke entity lookups.

**Rationale:** The path column represents the location within the entities folder. Including `entities/` is redundant since all entries in uuid-map are entities by definition.

**Correct format:**
```
| {uuid} | {type} | {type}/{shard}/{uuid} |
```

**Wrong format:**
```
| {uuid} | {type} | entities/{type}/{shard}/{uuid} |  ❌
```

**Action taken:** Updated entity-management SKILL.md with explicit warning and wrong example.

---

## 2026-02-03: Remove blocks/blocked-by Relationship

**Decision:** Remove the `blocks/blocked-by` relationship pair from the schema. Only `enables/depends-on` remains for dependencies.

**Context:** During Substrate Board UI development, we discussed how to visualize dependencies. This led to examining the semantic difference between `blocks/blocked-by` and `enables/depends-on`.

**Rationale:** They express the same underlying structure with different framing:
- `enables/depends-on` = the structural prerequisite relationship (planning perspective)
- `blocks/blocked-by` = the same relationship expressed when actively trying to proceed (execution perspective)

"Blocked" is a runtime/contextual interpretation: if B depends-on A and A isn't complete while B is active, A is effectively blocking B. The UI should derive "blocked" status rather than storing it as a separate relationship.

**Trade-offs:**
- One less relationship to reason about
- UI must compute "blocked" status from dependency + completion state
- Avoids philosophical confusion about when to use which
- Data model stays simple, UI interprets context

**Alternative considered:** Keep both relationships — `blocks` for unplanned impediments, `depends-on` for planned prerequisites. Rejected because the distinction is contextual, not structural.
