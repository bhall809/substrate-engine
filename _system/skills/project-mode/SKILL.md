---
name: project-mode
description: >
  Beverly's disciplined arc for building a substantial deliverable with AI when
  the shape of the deliverable isn't fully known yet. Trigger when she says
  "Start Project Mode," "Project mode," "let's build X," or otherwise signals a
  project-shaped task: designing a system (a Tana structure, a new agent, a
  skill, a schema change), creating a reusable artifact (a checklist, template,
  SOP, brief), or working a complex legal/business question where thinking must
  precede production. The tell: a single prompt would produce a mediocre result
  because the hard part is figuring out WHAT to build, not building it. Do NOT
  trigger for quick asks, lookups, one-offs, or work a specialized skill already
  owns end-to-end (a clean legal letter → legal-correspondence). Project Mode is
  a front-end that hands off to those skills once the shape is clear.
---

# Project Mode

Beverly's own build methodology — descended from Tiago Forte's AIRPORT, tailored
to how she works and to the fact that she runs it inside Claude Code (not bare
ChatGPT). It exists to elicit better thinking before production: define the
artifact, interview toward it, gate the decision, build, then route what was
learned back into Substrate so the next build starts further ahead.

## Core principle

Separate **thinking from writing**. The biggest failure mode is moving from
understanding to production too fast. Nothing gets built until the artifact is
pinned and reflected back. The AI drives the discovery; Beverly's job shifts from
composing a giant prompt to answering good questions.

## What Claude Code already gives you — don't rebuild it

Three AIRPORT steps are native to this environment. Lean on them; do not
re-implement them in this skill:

- **Plan** → use Claude Code **Plan mode**. It *is* the plan-before-executing gate.
- **Restart** → Beverly's fresh-daily-session + end-of-session-save habit. If
  context has drifted mid-build, start a clean session carrying only the distilled
  requirements.
- **Options** → generating 2–3 approaches with trade-offs is default behavior.

This skill builds only the moves Beverly does not already have: **Artifact,
Interview, the two Gates, and Integrate-into-Substrate.**

**Why these three aren't arbitrary borrowings.** Plan → Options → Restart is the
same discipline already encoded as `engagement_mode` in Substrate's own schema
(wander → explore → experiment → execute) — explore freely, compare approaches,
only then commit; premature commitment is a validation error there, not just bad
practice. And Restart's premise — that the first pass is for learning, not the
final version — is Principle 2, **Start, Then Iterate**, applied to a build
session: start before you're fully ready, treat the first conversation as an
experiment rather than a commitment to getting it right immediately. Both
connections surfaced independently (via a Beverly/ChatGPT-Atlas synthesis
session, 2026-07-07) from watching how Project Mode is actually used — which is
itself evidence the arc fits her existing operating model rather than imposing a
foreign one.

## When to reach for it / when not to

**Reach for it when:** building a system; creating a reusable artifact used across
matters; working a complex question needing thought before a decision.

**Skip it when:** the shape is already clear (just ask); it's a one-off or lookup;
a specialized skill already carries it end-to-end. For a legal letter whose shape
*isn't* clear, enter Project Mode to figure out what the letter must be, then hand
the drafting to `legal-correspondence`.

Project Mode is not the default way to engage. It is a mode Beverly enters
deliberately for project-shaped work.

---

## The Arc — five phases

### Phase 1 — Understand *(no building)*

1. **Artifact.** Pin the exact deliverable as a tangible noun. "A Georgia
   commercial-lease issue checklist," not "help me organize leases." A tangible
   artifact narrows the solution space immediately. If Beverly opens with a
   problem ("help me with X"), convert it to an artifact before proceeding.
2. **Interview.** Drive. Ask questions until you are ~95% confident you could
   build the right artifact — surfacing the context she would otherwise omit
   (people forget ~80% of what's relevant because they don't know it's relevant).
   Ask about her actual workflow, pain points, how she thinks about this class of
   thing, and what "good" looks like to her.

   **Confidentiality gate — input side.** If the artifact touches a client
   matter, run the Interview under the pseudonymization protocol: matter code,
   fictional party names, identifiers stripped, the legal question kept intact.
   Flag it if real names or facts appear. (See PREFERENCES: AI-Safe Brainstorming
   Protocol and Matter Code Convention.)

### Phase 2 — Model *(checkpoint)*

Reflect the artifact back: *"Here's the artifact as I understand it, and here's
what I heard."* Then ask **"Did I miss anything?"** Nothing gets designed until
this lands. This is the first of two places you fire the self-check (below).

### Phase 3 — Design

- **Resources.** Tell Beverly what inputs would make the result dramatically
  better — statutes (e.g. the relevant OCGA sections), precedents, a style guide,
  a Tana export, prior drafts — and name which existing skill this will hand off
  to at build time.
- **Unknowns.** Name explicitly what you still don't know.
- **Plan + Options.** Use **Plan mode** for the plan. Offer 2–3 approaches
  (typically minimal / balanced / built-out) with honest trade-offs.
- **Decision Gate** *(before any build)* — fire the self-check, then ask:
  - Do we know enough to proceed?
  - Is there a **simpler artifact** that accomplishes the goal?
  - Should we pause, or change direction?

  This gate is Beverly's Clarity Lenses and Hold-the-Tension principle in
  workflow form — and it is the don't-overbuild rule enforced by the framework
  itself. Many projects don't need more work; they need a decision.

### Phase 4 — Build

Only now produce the artifact. **Hand off to the specialized workflow** where one
exists rather than reinventing it:

- Legal correspondence → `legal-correspondence`
- Contract/agreement drafting → iterative, provision-by-provision, consistency
  pass first, redline-ready + severity-ranked issues
- Personal essay → paragraph-by-paragraph, numbered options, Beverly driving;
  load the Karlsson style profile if an influence lens is wanted
- Tana / Substrate structure → the relevant entity/schema/Tana workflow

**Decomposition patterns (earned in live runs — reach for these when the artifact
is a multi-part structure, e.g. a matter mapped onto the Status Board):**

- *Reusable template, instantiated per party.* Where several parties/items take an
  identical set, define the set once and instantiate it in parallel; vary only the
  small differing piece (e.g. a per-party count).
- *Combined-vs-standalone parallel sets.* A multi-party deal may split so some
  parties share ONE combined document set while another stands alone with its OWN
  full duplicate — structure as parallel groups under the same tier, labeled by
  which parties each covers. Don't assume one shared set for all.
- *Skeleton-complete, depth-where-live.* Build the FULL map (every tier/ticket) so
  the whole thing is scoped and legible, but only materialize leaf tasks under the
  parts that are actually live; leave the rest as neutral placeholder tickets
  (marked 📑, "expand at closing prep") and explode them when that part goes live.
  Prevents burying live work under dozens of idle tasks — and serves the Decision
  Gate's "simpler artifact?"
- *Confidentiality cross-reference.* Privileged specifics live in a private node
  whose *title is neutral* (details in children); the shared surface shows only a
  neutral reference (+ optional checkbox to track implementation). Guards human/
  visual exposure — NOT agent access, which a loaded workspace always permits.

**Document-review pattern (earned in live runs — reach for this when the artifact
is a transaction-document review with a client-communication component):**

1. **Solo Tana meeting.** Beverly's low-friction first-pass capture — a voice-agent
   conversation with herself, raw reactions to the documents, no structure imposed
   yet. Happens outside this skill, before Phase 4 build work starts here.
2. **Structured review with Claude.** The `contract/agreement drafting` hand-off
   above: iterative, provision-by-provision, consistency/contradiction pass first,
   redline-ready language, severity-ranked issues.
3. **Client-dialogue script (available when warranted, not automatic).** A Loom
   script explaining documents/provisions to the client in accessible,
   non-overwhelming language, built from step 2's output. Not every provision
   earns one — flag *which* areas benefit from advance client explanation versus
   which Beverly should just handle without it, so the step doesn't add overhead
   where the value isn't there. Naming that split is Claude's job going into this
   step, not just the drafting once told.

If context has drifted, Restart into a fresh session first.

### Phase 5 — Validate & Integrate

- **Test** against one real use case. Testing loops back to revision — it does
  not end the process. Expect at least one revision pass.
- **Integrate into Substrate.** This is where Beverly's version beats Tiago's:
  the "second brain" the Integrate step points at is one she already runs.
  - Route the build's design decisions into a `decision` entity (use the
    `decision-traces` skill).
  - Update the **Bulletin Board**: retire the pending thread this build resolved;
    add any follow-on.
  - Save reusable artifacts as `document`/`reference` entities so the next build
    starts from what worked.

  **Confidentiality gate — output side.** Only `workflow`-classified content
  routes to Substrate. `privileged` client facts, party names, and
  matter-specific analysis stay in Tana or the session. Set `confidentiality` at
  creation on any new entity.

---

## Your standing job throughout — active collaborator, not generator

Fire this self-check **at the two gates** (the Model checkpoint and the Decision
Gate) — not before every step, which produces performative hedging Beverly has
explicitly asked you to avoid. The four that earn their place:

- What am I **assuming**?
- What information is **missing**?
- Is Beverly solving the **right problem**?
- Is there a **simpler artifact**?

Give a recommendation, not a survey. Beverly wants to stay in the decision loop —
name the fork and your pick, don't offload the whole decision back to her.

---

## Status

**v1 (lean).** The core arc only. Deliberately does not encode domain-specific
Interview presets, a library of past builds, or a fuller self-check litany — those
get added only when live use reveals a felt gap, not because they sound good on
paper (the Decision Gate applied to the framework itself). Iterate from friction.

**Iteration ledger.** Friction notes and queued v1.1 candidates (the lens-at-gate
map — Clarity Lenses / Wisdom Audit invoked at the right gates; the bounded Joe
Hudson fit for coaching-shaped artifacts) live in Substrate decision `498c4f05` →
`build-spec.md`. Fold them into this skill at true Integrate, AFTER a build
completes — not before. **First full run: PRIEN-LAKE-AH decomposition (2026-07-08)
— completed;** the four Decomposition patterns above were folded in from it (see
decision `8fcb7c59`). Older v1.1 candidates (lens-at-gate map, Joe Hudson fit,
Known/Unknown vocabulary) remain parked — this run didn't exercise them, so the
skill's own Decision Gate holds them until one does.
