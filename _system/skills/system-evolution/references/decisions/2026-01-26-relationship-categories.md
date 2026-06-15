# Decision Record: Relationship Categories

**Date:** 2026-01-26
**Status:** Resolved
**Topic:** How to categorize relationships, especially around authorship and provenance

## Table of Contents

- [Context](#context)
- [The Problem](#the-problem)
- [Working Examples](#working-examples)
- [Considered Options](#considered-options)
- [The Functional Test](#the-functional-test)
- [The Biological Parentage Test](#the-biological-parentage-test)
- [Final Decisions](#final-decisions)
- [The Four Categories (Final)](#the-four-categories-final)
- [Why Not Five Categories?](#why-not-five-categories)
- [Files Updated](#files-updated)
- [Future Considerations](#future-considerations)

---

## Context

We were reviewing the relationship categories and found inconsistencies between documents. This led to a deeper examination of what the categories actually mean and how relationships should be assigned to them.

The original four categories were:
- Hierarchical
- Causal
- Provenance
- Associative

---

## The Problem

The category "Provenance" was conflating two different concepts:

1. **Contextual origin** — The setting or circumstance where something arose (e.g., "the note came from a meeting")
2. **Generative origin** — The agent who created something (e.g., "Miguel wrote the book")

This conflation caused confusion about where `authored-by` belonged.

---

## Working Examples

We tested five examples to clarify the distinctions:

### Example 1: A note comes from a meeting

**Analysis:** The meeting was the *context* where the note was born—the circumstance of its creation. The meeting didn't *make* the note; someone in the meeting made it. The meeting is *where/when* it happened.

**Verdict:** Contextual origin.

### Example 2: Bob comes from Jack (biologically)

**Analysis:** Jack is the *origin*—Bob literally descended from Jack's genetic material. Jack didn't just *witness* Bob's creation; Jack was a necessary causal ingredient.

**Verdict:** Generative origin. But also hierarchical (`child-of`/`parent-of`).

### Example 3: Bob was raised in Jack's household

**Analysis:** Jack provided the *context* for Bob's upbringing. Jack didn't create Bob; Bob developed *within* Jack's sphere. This feels more like the meeting example—circumstantial, not causal origin.

**Verdict:** Hierarchical (under Jack's authority) and/or Associative (guardian role).

### Example 4: Miguel wrote the book

**Analysis:** Miguel is the *creator*—the agent who brought the book into existence. Without Miguel's action, no book.

**Verdict:** Generative origin. Similar to the biological father case.

### Example 5: That quote is from Brad, in Book X

**Analysis:** Two relationships here:
- Quote **authored-by** Brad (Brad said/wrote these words) — Generative
- Quote **comes-from** Book X (the quote appears in this source) — Contextual

The book is the *context* where you find the quote. Brad is the *originator* of the words.

---

## Considered Options

### Option A: Add a fifth category (Generative)

Create a new category for agent-as-origin relationships:
- Hierarchical
- Causal
- Contextual (non-agent origins)
- Generative (agent origins)
- Associative

**Problem:** Would `authored-by` chain usefully? "All things authored by things authored by Miguel" → Books don't author things. Recursion produces garbage.

### Option B: Allow categories to overlay

Let relationships belong to multiple categories simultaneously.

**Problem:** Defeats the purpose of categories. Categories exist to enable bulk yet nuanced search behavior. Overlays would create combinatorial complexity and unclear query behavior.

### Option C: Use functional test to assign categories

Instead of semantic meaning, ask: *Does recursive traversal of this relationship category produce useful results?*

---

## The Functional Test

This became the deciding insight.

| Category | Recursive traversal produces... |
|----------|--------------------------------|
| Hierarchical | Useful tree of everything nested under/above something |
| Causal | Useful chain of effects and consequences |
| Contextual Ancestry | Useful chain of origins and sources |
| Associative | Garbage. These are terminal connections. |

**Key realization:** `authored-by` belongs in Associative not because authorship isn't "generative" semantically, but because following `authored-by` recursively produces meaningless results. Books don't author things. The chain terminates.

Same logic applies to:
- `assigned-to` — Tasks don't get assigned to things
- `attended` — Meetings don't attend things
- `stakeholder-in` — Campaigns don't have stakes in people

These are all **terminal relationships**: they connect an entity to something, full stop. No useful chain.

---

## The Biological Parentage Test

This example helped validate the functional approach:

- "All ancestors of Bob" (recursive `parent-of`) → Jack, Jack's parents, etc. → **Useful!**
- "All things Bob's father authored" → Not a traversal, just a filter on a different entity

So `child-of`/`parent-of` belongs in **Hierarchical** because *that chain is useful*. The fact that parentage is also "generative" semantically doesn't matter functionally.

---

## Final Decisions

### 1. Rename "Provenance" to "Contextual Ancestry"

The new name clarifies that this category is about *non-agent origins*—the contexts, circumstances, and source materials in something's history.

### 2. Move `authored-by` to Associative

Because:
- A person isn't a context; they're an agent
- `authored-by` doesn't chain usefully (books don't author things)
- It's a role-based connection, like `assigned-to` and `attended`

### 3. Keep "Associative" as the category name, not "Terminal"

"Terminal" describes behavior, not meaning. The other category names describe *what the relationships are about*:
- Hierarchical → structure/containment
- Causal → purpose/effects
- Contextual Ancestry → origins/sources
- Associative → roles/connections

"Associative" tells you *what kind of relationship it is*. "Terminal" tells you *what you can't do with it*—that's an implementation detail for the description.

### 4. Add "terminal" to the Associative category description

> **Associative** — Role-based and general connections. These are terminal relationships: they connect entities directly without forming useful chains. Recursive traversal does not apply.

---

## The Four Categories (Final)

| Category | Question it answers | Recursive? | Examples |
|----------|---------------------|------------|----------|
| **Hierarchical** | What contains/organizes this? | Yes (down/up) | belongs-to, part-of, child-of, member-of |
| **Causal** | What purpose does this serve? What depends on it? | Yes (downstream/upstream) | leads-to, enables, blocks, advances |
| **Contextual Ancestry** | In what setting did this arise? | Yes (ancestry/descendants) | comes-from, derived-from |
| **Associative** | What roles or connections exist? | No (terminal) | authored-by, assigned-to, attended, relates-to, references |

---

## Why Not Five Categories?

We seriously considered adding "Generative" as a fifth category for agent-as-origin relationships. The functional test eliminated this need:

- If the relationship chains usefully → it belongs in an existing category based on what kind of chain it forms
- If the relationship doesn't chain → it belongs in Associative regardless of semantic meaning

The biological parent example proved this: `parent-of`/`child-of` is functionally Hierarchical (useful ancestor chain), even though it's semantically generative.

---

## Files Updated

- `relationship-management/SKILL.md`
- `relationships-template.md`
- `schema-dictionary.md`
- `entity-query/SKILL.md`
- `terminology.md`
- `concepts.md`
- `IDEAS-BACKLOG.md`

---

## Future Considerations

If we encounter relationships that don't fit cleanly, apply the functional test:

1. Does following this relationship recursively produce a meaningful set?
2. If yes: which existing category describes that chain best?
3. If no: it's Associative (terminal).
