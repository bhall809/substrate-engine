# Terminology Conventions

This document defines the current terminology for Substrate. Use these terms consistently across all skills and documentation.

## Table of Contents

- [Current Terms](#current-terms)
- [Folder Naming](#folder-naming)
- [Property Naming](#property-naming)
- [File Naming](#file-naming)
- [Relationship Direction Language](#relationship-direction-language)
- [Query Language](#query-language)
- [Universal Attributes](#universal-attributes)
- [Meta Status Values](#meta-status-values)
- [Special Markers](#special-markers)
- [Relationship Categories](#relationship-categories)
- [Historical Terms (Deprecated)](#historical-terms-deprecated)

## Current Terms

| Use This | Not This | Notes |
|----------|----------|-------|
| property | field | Properties are attributes or relationships on an entity |
| grouping | family | Ontological groupings categorize entity types |
| entities (folder) | vaults | The folder is named `entities/`, but "vault" is acceptable in prose (e.g., "the task vault") |
| staging | inbox | Folder for unprocessed items |
| meta.md | metadata.md | The file containing entity properties |
| uuid-map | uuid-index | The global UUID to path mapping |
| adjacency | relationships-index | The pre-computed relationship graph |
| sharded index | partitioned index | Type indexes split by UUID prefix |

## Folder Naming

| Folder | Convention | Example |
|--------|------------|---------|
| System folders | Underscore prefix | `_system/`, `_system/skills/` |
| Entity type folders | Lowercase, singular | `task/`, `person/` |
| Shard folders | Two hex characters | `55/`, `0e/` |
| Entity folders | Full UUID | `550e8400-e29b-41d4-a716-446655440010/` |

## Property Naming

| Convention | Example |
|------------|---------|
| Lowercase kebab-case | `start-date`, `assigned-to` |
| Relationships are verbs or verb phrases | `belongs-to`, `authored-by` |
| Attributes are nouns | `status`, `priority`, `format` |

## File Naming

| File Type | Convention | Example |
|-----------|------------|---------|
| Entity metadata | Always `meta.md` | `meta.md` |
| Schema files | Lowercase, descriptive | `types.md`, `attributes.md` |
| Index files | Lowercase with shard suffix | `_index-0-3.md` |
| Reference docs | Lowercase kebab-case | `index-operations.md` |
| Skill files | Always `SKILL.md` (uppercase) | `SKILL.md` |

## Relationship Direction Language

| Term | Meaning |
|------|---------|
| source | The entity initiating the relationship |
| target | The entity being related to |
| inverse | The reverse direction of a relationship |
| forward | From source to target |
| backward/upward | From target to source (via inverse) |

## Query Language

### Hierarchical Traversal
| User Says | Means |
|-----------|-------|
| "under", "beneath", "below" | All hierarchical relationships, recursive downward |
| "above", "over" | All hierarchical relationships, recursive upward |

### Causal Traversal
| User Says | Means |
|-----------|-------|
| "downstream" | All causal relationships, recursive forward (effects) |
| "upstream" | All causal relationships, recursive backward (causes) |

### Contextual Ancestry Traversal
| User Says | Means |
|-----------|-------|
| "ancestry" | Full origin chain, recursive backward |
| "descendants" | Full spawned chain, recursive forward |

### Specific Relationship
| User Says | Means |
|-----------|-------|
| "belongs to", "part of", "blocks", etc. | Single named relationship, one hop |

### Filtering
| User Says | Means |
|-----------|-------|
| "work", "horizons", "actors", etc. | Ontological grouping filter |
| "tasks", "documents", etc. | Type filter |

## Universal Attributes

| Attribute | Purpose |
|-----------|---------|
| `id` | UUID, unique identifier |
| `name` | Human-readable name |
| `type` | Entity type |
| `description` | 1-2 sentence explanation of what matters about this entity |
| `meta_status` | Relationship to system: `nascent`, `live`, `archived` |
| `created` | Creation timestamp |
| `modified` | Last modification timestamp |

## Meta Status Values

| Value | Meaning |
|-------|---------|
| `nascent` | Entity exists but isn't fully formed |
| `live` | Active, normal operating state |
| `archived` | Preserved but out of active circulation |

## Special Markers

| Marker | Used In | Meaning |
|--------|---------|---------|
| `[awaiting context]` | description | Agent lacked context at creation; update when context available |
| `[archived]` | relationship UUIDs | Target entity is archived; remove marker if restored |

## Relationship Categories

| Category | Description | Recursive? |
|----------|-------------|------------|
| Hierarchical | Structure/containment | Yes (up/down) |
| Causal | Purpose/effects | Yes (upstream/downstream) |
| Contextual Ancestry | Origins/sources (non-agent) | Yes (ancestry/descendants) |
| Associative | Roles/connections | No (terminal) |

### The Functional Test

To determine which category a relationship belongs to, ask: *Does following this relationship recursively produce useful results?*

- **Hierarchical:** "All things under X" → useful tree of nested entities ✓
- **Causal:** "All things downstream of X" → useful chain of effects ✓
- **Contextual Ancestry:** "Full ancestry of X" → useful chain of origins ✓
- **Associative:** "All things authored by things authored by X" → nonsense ✗

If recursive traversal produces garbage, the relationship is **terminal** and belongs in Associative, regardless of its semantic meaning.

**Example:** `authored-by` is semantically "generative" (the author created the thing), but it belongs in Associative because books don't author things—the chain terminates.

See also: `references/decisions/2026-01-26-relationship-categories.md` for the full reasoning.

## Historical Terms (Deprecated)

These terms appeared in earlier versions but should no longer be used:

| Deprecated | Replaced By | When Changed |
|------------|-------------|--------------|
| field | property | 2026-01-25 |
| family | grouping | 2026-01-25 |
| vaults (folder) | entities | 2026-01-25 |
| FSP / Functional System Paradigm | Substrate | 2026-01-26 |
| fsp-* (skill prefix) | substrate-* → no prefix | 2026-01-26 |
| .system, .skills (dot prefix) | _system, _skills (underscore prefix) | 2026-01-26 |
