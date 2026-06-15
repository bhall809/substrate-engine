# Skill Development Guidelines

Reference for building and maintaining Substrate skills, based on official documentation and project decisions.

## Naming

**Official recommendation:** Use gerund form (verb + -ing) for skill names, e.g., `managing-entities`, `querying-data`.

**Substrate decision:** We use noun-based names (e.g., `entity-management`, `entity-query`) because they read as capability domains rather than activities. This is intentional and internally consistent.

**Prefix:** No prefix. Originally used `substrate-` but removed it since these are the only skills in the package.

## File Structure

```
skill-name/
├── SKILL.md           # Main skill file (required)
└── [references/]      # Optional supplementary docs
```

## SKILL.md Constraints

- **Line limit:** Keep under 500 lines for optimal performance
- **Frontmatter required:**
  ```yaml
  ---
  name: skill-name
  description: When to use this skill...
  author: Nick Silhacek
  version: 0.1.0
  last_updated: YYYY-MM-DD
  ---
  ```

## Links and References

Markdown links in SKILL.md (e.g., `[reference.md](reference.md)`) are standard markdown — they don't give agents special "jump to file" capability. Agents still must explicitly read each file.

Links serve two purposes:
1. Human readability in markdown renderers
2. Clear signposting for agents about which files to read

**When to split into reference files:**
- Supplementary content exceeds ~200 lines
- Content is reusable across contexts
- Main SKILL.md is approaching 500 lines
- Technical detail would distract from the skill's core workflow

**Current status:** All Substrate skills are self-contained and well under limits. No splitting needed yet.

## Reference File Requirements

**Table of contents:** For reference files longer than 100 lines, include a table of contents at the top. This ensures agents can see the full scope of available information even when previewing with partial reads.

Format:
```markdown
## Table of Contents

- [Section One](#section-one)
- [Section Two](#section-two)
```

## Workflow Checklists

For complex multi-step operations, provide a copyable checklist that agents can track progress against. This helps agents maintain state through long workflows.

**When to include checklists:**
- Operations with 5+ sequential steps
- Workflows that modify multiple files/indexes
- Processes where skipping a step causes problems

**Format:**
````markdown
## Workflow Checklist

Copy this checklist and track your progress:

```
Operation Progress:
- [ ] Step 1: Description
- [ ] Step 2: Description
- [ ] Step 3: Description
```
````

**Skills with checklists:**
- `schema-evolution` — Rename Type, Merge Types, Rename Relationship
- `staging-intake` — Full intake workflow
- `system-validation` — Full validation, Quick index validation

## Conditional Workflows

For skills with branching logic, use decision points to guide agents:

```markdown
## Workflow

1. Determine the task type:

   **Creating new?** → Follow "Creation workflow" below
   **Modifying existing?** → Follow "Modification workflow" below

2. Creation workflow:
   - Step A
   - Step B

3. Modification workflow:
   - Step X
   - Step Y
```

**When workflows get large:** If a workflow branch exceeds ~50 lines or has many sub-steps, consider pushing it into a separate reference file. Direct agents to read the appropriate file based on the task.

## Skill Line Counts (as of 2026-01-26)

| Skill | Lines |
|-------|-------|
| system-validation | 280 |
| onboarding | 278 |
| archive-management | 220 |
| entity-management | 219 |
| entity-query | 200 |
| staging-intake | 189 |
| system-evolution | 186 |
| relationship-management | 184 |
| schema-evolution | 178 |
| entity-presentation | 121 |
| decision-traces | 92 |

## Checklist for New Skills

- [ ] Name follows project convention (noun-based, no prefix)
- [ ] SKILL.md under 500 lines
- [ ] Frontmatter complete (name, description, author, version, last_updated)
- [ ] Description explains when to use the skill
- [ ] Cross-references to other skills use correct names
- [ ] Added to CLAUDE.md skill table
- [ ] Added to VERSION.md skill list
