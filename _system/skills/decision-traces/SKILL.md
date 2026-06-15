---
name: decision-traces
description: Record why decisions were made. Use when documenting an unusual decision, looking up past precedents, or analyzing how similar situations were handled before.
author: Nick Silhacek
version: 0.1.0
last_updated: 2026-01-26
---

# Decision Traces

Decision traces capture not just what happened, but why. They form a searchable history of reasoning that can inform future decisions.

## When to Create a Trace

Create a decision trace when:
- Handling an ambiguous situation
- Granting an exception to normal rules
- Making a judgment call with multiple valid options
- The decision might be questioned later
- Establishing precedent for similar future cases

**Don't create traces for routine operations** (simple creates, standard updates) unless something unusual occurs.

## Always Trace These

Some operations should always be traced, regardless of whether they feel "unusual":

- **Schema modifications** — any change to `types.md`, `attributes.md`, or `relationships.md`
- **Skill updates** — any change to a `SKILL.md` file
- **Bulk operations** — creating, updating, or deleting 5+ entities in one operation
- **Archive operations** — moving entities or traces to archive
- **Config changes** — modifications to `preferences.md` or `user-state.md`
- **Index rebuilds** — running rebuild scripts to fix index drift

These are system-level changes that affect how Substrate behaves. Future agents need the reasoning.

## Trace Location

`/_system/logs/traces/{YYYY-MM-DD}-{short-description}.md`

The filename should be descriptive enough to find later.

## Trace Structure

```yaml
---
timestamp: {ISO datetime}
action: created | modified | linked | exception | other
entities_involved:
  - id: {UUID}
    type: {entity type}
---

# Decision: {Brief title}

## What Happened
{Brief description of the action taken}

## Context
{What information was examined to make this decision}

## Rules Applied
{Which policies or schema rules governed this decision}

## Precedents (if any)
{Links to prior traces that informed this decision}

## Exceptions (if any)
- Exception: {what rule was bypassed}
- Reason: {justification}
- Approved by: {user or policy that permitted it}

## Outcome
{Final result}
```

## Finding Precedents

When facing a decision similar to a past one:

1. **Search by keyword:** Look for traces mentioning similar concepts
2. **Search by entity:** Look for traces involving the same entity UUID
3. **Search by date:** Narrow to relevant time period
4. **Browse recent:** Check recent traces in `/_system/logs/traces/`

Traces in archives live in `/_system/archive/traces/{YYYY-MM}/`.

## Using Precedents

When a relevant precedent is found:
1. Read the full trace to understand the context
2. Assess whether the current situation is similar enough
3. If following precedent: mention it in your new trace
4. If deviating from precedent: document why

## Writing Good Traces

- **Be specific** - Future readers need to understand what you saw
- **Name the rules** - Reference specific schema constraints or policies
- **Link precedents** - Makes the decision chain traceable
- **Justify exceptions** - "Because the user said so" is valid, but note it

## Archiving

Old traces can be archived to `/_system/archive/traces/{YYYY-MM}/` to reduce clutter. This is user-initiated—see the archive-management skill for details.
