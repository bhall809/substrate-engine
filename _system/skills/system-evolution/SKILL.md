---
name: system-evolution
description: Meta-skill for evolving Substrate itself. Use when working on the system architecture, modifying skills, updating terminology, or making structural changes to how Substrate works. NOT for end users - only for system builders.
author: Nick Silhacek
version: 0.1.0
last_updated: 2026-01-26
---

# System Evolution

This skill guides meta-level changes to Substrate itself—the system that builds the system.

## About This Skill

This is a meta-skill for evolving Substrate itself. It provides context on:
- How to modify existing skills
- Terminology conventions and consistency
- Architectural decisions and their rationale
- Cross-skill dependencies

Reference documents in `references/` contain detailed conventions:
- `terminology.md` — Current terms and naming patterns
- `decisions-log.md` — Why things are the way they are
- `cross-skill-dependencies.md` — What affects what
- `IDEAS-BACKLOG.md` — Future features and deferred ideas

## Scope

This skill covers:
- Modifying existing skills
- Adding new capabilities to the system
- Maintaining terminology consistency
- Documenting architectural decisions
- Cross-skill dependency management
- Testing and validating changes

This skill does NOT cover:
- Normal entity operations (use entity-management)
- Schema changes like adding types/attributes (use schema-evolution)
- Querying or presenting entities (use entity-query, entity-presentation)

## Before Making Changes

### 1. Understand Cross-Skill Dependencies

Skills reference each other and shared resources. Before modifying anything:

| Resource | Referenced By |
|----------|---------------|
| `/_system/schema/types.md` | entity-management, entity-query, schema-evolution, staging-intake |
| `/_system/schema/attributes.md` | entity-management, schema-evolution, staging-intake |
| `/_system/schema/relationships.md` | relationship-management, schema-evolution, entity-query, staging-intake |
| `/_system/index/uuid-map.md` | entity-management, entity-query, entity-presentation, relationship-management, schema-evolution, staging-intake, system-validation, archive-management |
| `/_system/index/adjacency.md` | entity-query, relationship-management, schema-evolution, staging-intake, system-validation, archive-management |
| Index operations | All skills reference index-operations.md patterns |

For full dependency mapping, see `references/cross-skill-dependencies.md`.

### 2. Check Terminology Conventions

See `references/terminology.md` for current terms. Key conventions:
- "property" not "field"
- "grouping" not "family"
- "entities" folder, but "vault" is acceptable in documentation
- `_system/` and `_system/skills/` with underscore prefix

### 3. Review Architecture Decisions

See `references/decisions-log.md` for why things are the way they are. Don't undo past decisions without understanding them.

## Modifying Skills

### Editing a SKILL.md

1. Read the current skill completely
2. Identify what needs to change and why
3. Check if the change affects other skills
4. Make the edit
5. Verify terminology consistency
6. Update any related reference docs
7. Log the change in `references/decisions-log.md` if it's architectural

### Adding Content to a Skill

When adding new sections or capabilities:
- Match the existing formatting style
- Use consistent header levels
- Include examples where helpful
- Reference schema/index files rather than hardcoding values that could drift

### Removing Content from a Skill

Before removing:
- Check if other skills reference it
- Check if it documents behavior that still exists
- Consider if it should move elsewhere rather than be deleted

## Adding New Capabilities

### New Index Type

1. Define the purpose and structure
2. Add to `workspace-setup` folder structure
3. Add initialization template
4. Add operations to `references/index-operations.md`
5. Update relevant skills to use the new index
6. Log decision in `references/decisions-log.md`

### New Relationship Category

1. Define the category and its relationships in `relationships-template.md`
2. Update `relationship-management` with the new category
3. Update `entity-query` if it affects traversal behavior
4. Log decision

### New Entity Type

This is handled by `schema-evolution`, not this skill. But if the new type requires new *capabilities* (new attributes patterns, new relationship patterns), those architectural changes belong here.

## Testing and Validation

### After Skill Changes

1. Read the modified skill end-to-end - does it flow logically?
2. Check for terminology consistency
3. Verify all file path references are correct
4. Confirm cross-references to other skills/docs are accurate

### After Structural Changes

1. Trace through a sample operation (e.g., "create a task") using the updated skills
2. Verify indexes would be updated correctly
3. Check that nothing references removed/renamed resources

### Regression Check

When changing shared resources (schema files, index formats), verify:
- All skills that reference it still make sense
- Examples in skills still match the new format
- No orphaned references

## User-Facing Documentation

User documentation lives in `/README/` (separate from skills):

| File | Purpose | Update When |
|------|---------|-------------|
| `welcome.md` | First introduction for new users | Onboarding flow changes |
| `concepts.md` | Core concepts explained | Entity model, relationships, or schema concepts change |
| `faq.md` | Common questions and answers | New features, changed behaviors, or common confusion points |
| `schema-dictionary.md` | Auto-generated from schema | Regenerate when types/attributes/relationships change |

### Updating User Docs

When skill changes affect user-visible behavior:
1. Identify which doc(s) need updates
2. Write from the user's perspective (not system internals)
3. Use the same terminology as the skills (see `references/terminology.md`)
4. Keep examples practical and conversational

### Schema Dictionary

`schema-dictionary.md` should be regenerated (not manually edited) when the schema changes. It pulls from:
- `/_system/schema/types.md`
- `/_system/schema/attributes.md`
- `/_system/schema/relationships.md`

## Reference Documents

**In this skill's folder:**
- `references/terminology.md` - Current terms and conventions
- `references/decisions-log.md` - Architectural decisions and rationale
- `references/cross-skill-dependencies.md` - What references what
- `references/IDEAS-BACKLOG.md` - Future features and deferred ideas

## Ideas Backlog

When you encounter ideas worth preserving but not ready for implementation:
1. Read `references/IDEAS-BACKLOG.md` to see existing ideas
2. Add new ideas with context, the idea itself, why deferred, and when to revisit
3. Reference related ideas when making architectural decisions

This prevents losing good ideas while keeping current work focused.

## Important Notes

- This skill should NOT be included in end-user deployments
- Changes here affect the entire system - proceed carefully
- When in doubt, document first, implement second
- Log significant decisions for future reference
