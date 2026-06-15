# Ideas Backlog

Future features, enhancements, and considerations. These are ideas worth preserving but not ready for implementation yet.

## Table of Contents

- [Training / Philosophy Skill](#training--philosophy-skill)
- [Automatic Threshold-Based Archiving](#automatic-threshold-based-archiving)
- [Localized Display Names](#localized-display-names)
- [Named Agent Persona](#named-agent-persona)
- [Relationship Categories for Non-Western Languages](#relationship-categories-for-non-western-languages)
- [Git-Based Version Control](#git-based-version-control)

---

## Training / Philosophy Skill

**Context:** Beyond the onboarding skill (which teaches users how to use the system), there could be value in deeper training that teaches users how to *think* about things - the philosophy behind the system.

**The idea:** Much of Substrate bakes in philosophical decisions:
- Function precedes form
- Identity emerges from relationships
- Meaning is contextual, not intrinsic
- Structure should emerge from behavior, not be imposed

Some users might benefit from understanding these principles, not just the mechanics. This could help them:
- Make better decisions about how to structure their own content
- Understand *why* the system works the way it does
- Apply the thinking beyond Substrate

**Possible approaches:**
- A separate "philosophy" or "thinking" skill
- Deeper optional tracks within onboarding
- Reference documentation explaining the paradigm
- Interactive exercises that demonstrate the principles

**Questions to resolve:**
- How do you teach philosophy without being preachy?
- Is this opt-in for power users, or valuable for everyone?
- What's the right format - conversational, documentation, or interactive?

---

## Automatic Threshold-Based Archiving

**Context:** Currently all archiving is user-triggered. We discussed but deferred automatic triggers.

**The idea:** Scheduled jobs or agent hooks that:
- Check trace age/count periodically
- Offer to archive when thresholds exceeded
- Could run at session start or after certain operations

**Why deferred:** No infrastructure for scheduled jobs yet. Would need:
- A trigger mechanism (cron-like, or hook-based)
- Clear user control over automation
- Graceful handling when thresholds are exceeded

**When to revisit:** When Substrate has more operational infrastructure, or if users consistently forget to archive and clutter becomes a problem.

---

## Localized Display Names

**Context:** Schema uses English canonical names (types, relationships), but users may speak other languages.

**The idea:** Add per-language display names to schema:
```yaml
project:
  display:
    en: Project
    de: Projekt
    it: Progetto
```

Canonical names stay English for system operations; display adapts to user preference.

**Why deferred:** Current users are English-speaking or comfortable with English terms. Cross-lingual intent matching works reasonably well without this.

**When to revisit:** When expanding to non-English-primary markets, or if user feedback indicates friction.

---

## Named Agent Persona

**Context:** The user-facing documentation speaks as "I" to the user ("Just tell me what to do"). This raises the question: should the primary agent have a name?

**The idea:** A named persona serves multiple purposes:
1. **Explicit trigger** - "Sam, create a task" guarantees correct skill routing when natural language is ambiguous
2. **Relationship building** - Users may feel more comfortable with a named assistant
3. **Reinforcement mechanism** - The name becomes salient to agents, helping them stay in character
4. **Consistency** - All user-facing docs can reference the same persona

**Name alternatives to consider:**
- **Sam** - Simple, friendly, gender-neutral
- **Kit** - Short, memorable, suggests "toolkit"
- **Kai** - Gender-neutral, clean sound
- **Lex** - Suggests language/intelligence
- **Nova** - Fresh, forward-looking
- **Sol** - Grounded, warm
- **Atom** - Suggests fundamental building blocks (fits the Substrate metaphor)
- **Clay** - Malleable, foundational (also fits Substrate metaphor)

**Why deferred:** Want to test natural language routing first without the crutch of explicit triggers. Also want to see if users naturally want a named persona or if "I/me" feels sufficient.

**When to revisit:**
- After user testing reveals where natural language consistently fails
- If users express desire for a more personal interaction
- When implementing more sophisticated agent reinforcement

---

## Relationship Categories for Non-Western Languages

**Context:** The four relationship categories (Hierarchical, Causal, Contextual Ancestry, Associative) map well to European languages but may not capture concepts in Chinese, Vietnamese, Arabic, etc.

**The idea:** Some languages/cultures may want relationship types that don't fit neatly:
- "Associated through shared context"
- Relationships with different temporal semantics
- Concepts that don't have direct English equivalents

**Possible approach:** Make the category system extensible, or treat categories as suggestive rather than exhaustive.

**Why deferred:** Current users are in European language contexts. System works for them.

**When to revisit:** When expanding to Asian or other markets, or if users request relationship types that feel awkward in current categories.

---

## Git-Based Version Control

**Context:** Workspace backup options include Git, but large binary files require Git LFS.

**The idea:** First-class Git integration:
- Automatic commits on significant changes
- Branch per major initiative
- Easy rollback to previous states
- Conflict resolution for collaborative workspaces

**Challenges:**
- Large files (images, PDFs, videos) don't play well with Git
- Merge conflicts in YAML could be messy
- Would need careful .gitignore for temporary files

**Why deferred:** Simple folder copying and cloud sync work for now. Git adds complexity.

**When to revisit:** When users need version history, collaboration features, or when workspace sizes make full-folder backup impractical.

---

*Add new ideas below this line*

---

## Cowork vs Claude Code Positioning

**Context:** Substrate works in both Claude Cowork and Claude Code, but they serve different purposes.

**The idea:** Position the two contexts as complementary:

| Context | Purpose |
|---------|---------|
| **Cowork** | Get stuff done — manage entities, track work, daily operations |
| **Claude Code** | Build stuff — custom scripts, dashboards, interfaces on your data |

Substrate is the shared data layer. Same files, same schema, different interaction modes.

**Implications:**
- Documentation could highlight both use cases
- Skills might be optimized for one context or the other
- Claude Code users might want additional "builder" skills (generate UI, create CLI tools)
- CLAUDE.md might need context-specific sections (Cowork permissions vs Code permissions)

**Why noted:** Came up during 2026-02-11 session. Worth exploring as part of positioning and documentation.

---

---

## Entity-Level Activity History

**Context:** Currently we have system-level decision traces (`_system/logs/traces/`) for unusual operations. But there's no per-entity history showing status changes and decisions over time.

**The idea:** Each entity (especially work entities like projects, tickets, tasks) could have an activity log showing:
- Status transitions (pending → active → completed)
- Context/reason for each change
- Who/what made the change
- Timestamp

**Possible approaches:**
1. **Separate file per entity** — `activity.md` alongside `meta.md` in the entity folder
2. **Embedded in meta.md** — An `activity` property with structured entries
3. **Centralized log** — Entity activity stored in `_system/logs/entity-activity/` with references

**Questions to resolve:**
- How verbose? Every change, or just significant ones?
- Who writes the entries — agent automatically, or user-initiated?
- Storage format — YAML list, markdown log, or structured file?

**Why deferred:** Core functionality first. This is an enhancement for auditability and context preservation.

**When to revisit:** After the basic workflow is proven and there's a need to trace entity history.

