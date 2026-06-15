# Cross-Skill Dependencies

This document maps what references what across the Substrate skills. Use this when modifying shared resources to understand impact.

## Table of Contents

- [Schema Files](#schema-files)
- [Index Files](#index-files)
- [Config Files](#config-files)
- [Log Files](#log-files)
- [Reference Documents](#reference-documents)
- [Skill-to-Skill References](#skill-to-skill-references)
- [Summary: High-Impact Files](#summary-high-impact-files)
- [Skill Inventory](#skill-inventory)

## Schema Files

### /_system/schema/types.md

| Skill | How It's Used |
|-------|---------------|
| entity-management | Get valid types before creating entity |
| entity-query | Filter by ontological grouping |
| schema-evolution | Add/rename/merge types |
| workspace-setup | Initialize with default types |
| staging-intake | Infer entity type from staged content |
| system-validation | Verify entities conform to defined types |

**If you change types.md format:** Update all six skills.

### /_system/schema/attributes.md

| Skill | How It's Used |
|-------|---------------|
| entity-management | Validate attributes, get status values |
| schema-evolution | Add/remove/modify attributes |
| workspace-setup | Initialize with default attributes |
| staging-intake | Extract and validate attributes from staged content |
| system-validation | Check required attributes, valid values |

**If you change attributes.md format:** Update all five skills.

### /_system/schema/relationships.md

| Skill | How It's Used |
|-------|---------------|
| relationship-management | Validate relationships, get inverses |
| entity-query | Understand relationship categories for traversal |
| schema-evolution | Add/rename/remove relationships |
| workspace-setup | Initialize via relationships-template.md |
| staging-intake | Infer relationships from staged content |
| system-validation | Verify relationship names are valid |

**If you change relationships.md format:** Update all six skills.

## Index Files

### /_system/index/uuid-map.md

| Skill | How It's Used |
|-------|---------------|
| entity-management | Register new entities, look up paths, remove on delete |
| entity-query | Resolve UUIDs to paths |
| entity-presentation | Resolve UUIDs for display |
| relationship-management | Look up entity paths |
| schema-evolution | Update paths during type renames/merges |
| staging-intake | Register newly created entities |
| system-validation | Verify all entities are registered, detect orphans |
| archive-management | Remove entries when archiving entities |

**If you change uuid-map format:** Update all eight skills.

### /_system/index/adjacency.md

| Skill | How It's Used |
|-------|---------------|
| entity-query | Traverse relationships without loading meta.md |
| relationship-management | Update when relationships change |
| schema-evolution | Find entities by relationship, update during renames |
| staging-intake | Register inferred relationships |
| system-validation | Verify bidirectional integrity |
| archive-management | Remove entries when archiving entities |

**If you change adjacency format:** Update all six skills.

### /_system/index/pending.md

| Skill | How It's Used |
|-------|---------------|
| entity-management | Queue index updates |
| relationship-management | Queue adjacency updates |
| schema-evolution | Queue batch updates during migrations |
| staging-intake | Queue updates for batch processing |

**If you change pending format:** Update all four skills.

### /entities/{type}/_index-{shard}.md

| Skill | How It's Used |
|-------|---------------|
| entity-management | Add/update/remove entity entries |
| entity-query | List entities by type, search by name |
| schema-evolution | Find entities during migrations |
| workspace-setup | Create empty sharded indexes |
| staging-intake | Register newly created entities |
| system-validation | Verify entries match actual entities |
| archive-management | Remove entries when archiving entities |

**If you change sharded index format:** Update all seven skills.

## Config Files

### /_system/config/preferences.md

| Skill | How It's Used |
|-------|---------------|
| workspace-setup | Create with defaults during initialization |
| entity-management | Check confirm_destructive_actions |
| entity-presentation | Check verbosity, date_format |

**If you change preferences format:** Update affected skills.

### /_system/config/user-state.md

| Skill | How It's Used |
|-------|---------------|
| workspace-setup | Create with defaults during initialization |
| onboarding | Track onboarding status and completed topics |
| entity-presentation | Read comfort_level for verbosity adaptation |

**If you change user-state format:** Update affected skills.

## Log Files

### /_system/logs/traces/

| Skill | How It's Used |
|-------|---------------|
| decision-traces | Create, query, and index traces |
| archive-management | Archive old traces, maintain archive index |
| system-validation | May create traces when auto-repairing |
| staging-intake | Create traces for ambiguous intake decisions |

**If you change trace format:** Update all four skills.

### /_system/logs/index.md

| Skill | How It's Used |
|-------|---------------|
| decision-traces | Maintain index of active traces |
| archive-management | Update when archiving traces |

**If you change trace index format:** Update both skills.

## Reference Documents

### references/index-operations.md

| Location | Purpose |
|----------|---------|
| workspace-setup/references/ | Defines abstract index operations |

| Skill | Relationship |
|-------|--------------|
| All skills | Should follow these patterns (conceptually) |

**If you change index operations:** All skills may need review.

### references/relationships-template.md

| Location | Purpose |
|----------|---------|
| workspace-setup/references/ | Full template for relationships.md |

| Skill | Relationship |
|-------|--------------|
| workspace-setup | Uses to create relationships.md |
| relationship-management | Should stay in sync |

**If you change relationship definitions:** Update both template and relationship-management skill.

### references/preferences-schema.md

| Location | Purpose |
|----------|---------|
| workspace-setup/references/ | Defines preferences structure and defaults |

| Skill | Relationship |
|-------|--------------|
| workspace-setup | Uses to create preferences.md |
| All skills that read preferences | Should understand the schema |

**If you change preference definitions:** Update schema doc and all consuming skills.

### references/user-state-schema.md

| Location | Purpose |
|----------|---------|
| workspace-setup/references/ | Defines user state structure |

| Skill | Relationship |
|-------|--------------|
| workspace-setup | Uses to create user-state.md |
| All skills that read/write user state | Should understand the schema |

**If you change user-state definitions:** Update schema doc and all consuming skills.

## Skill-to-Skill References

| Skill | References |
|-------|------------|
| entity-query | Hands off to entity-presentation for display |
| entity-management | May trigger relationship-management for bidirectional updates |
| schema-evolution | Uses patterns from entity-management, relationship-management |
| staging-intake | Uses entity-management to create entities, relationship-management for links |
| staging-intake | May trigger schema-evolution if new type needed |
| staging-intake | Creates traces via decision-traces patterns |
| system-validation | May trigger archive-management during maintenance |
| system-validation | Creates traces via decision-traces for repairs |
| archive-management | Updates indexes like entity-management does |

## Summary: High-Impact Files

Changing these affects the most skills:

1. **uuid-map.md format** - 8 skills
2. **sharded index format** - 7 skills
3. **types.md format** - 6 skills
4. **relationships.md format** - 6 skills
5. **adjacency.md format** - 6 skills
6. **attributes.md format** - 5 skills

Always check this document before modifying shared resources.

## Skill Inventory

Complete list of Substrate skills:

| Skill | Purpose |
|-------|---------|
| workspace-setup | Initialize new Substrate workspace |
| entity-management | CRUD operations on entities |
| entity-query | Find and retrieve entities |
| entity-presentation | Format entities for display |
| relationship-management | Link and unlink entities |
| schema-evolution | Modify types, attributes, relationships |
| decision-traces | Record and query decision history |
| system-evolution | Meta-skill for evolving Substrate itself |
| staging-intake | Process files from staging into entities |
| system-validation | Verify integrity, repair inconsistencies |
| archive-management | Archive old content, manage retention |
| onboarding | Guide new users through the system |
