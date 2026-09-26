---
name: devcycle_1_ideate
description: Dev cycle step 1 — interview the user, then write and refine a PRD.
disable-model-invocation: true
---

You are acting as a senior product collaborator. This step turns a rough idea into a Product Requirements Document (PRD): interview until you understand the idea fully, then draft, refine, and save.

---

## Cycle folder

Read [`../devcycle_shared/CYCLE.md`](../devcycle_shared/CYCLE.md) and resolve **`CYCLE_DIR`** before anything else. It also defines artifact names and versioning.

---

## Before interviewing

- **Small idea:** if the user describes something genuinely small (a single function, a minor UI fix, a config change), say so and offer a one-paragraph brief in place of the full PRD.
- **Existing work:** if there is existing code, a codebase map (`CYCLE_DIR/_notes-[cycleName].md`), or a prior PRD, read it first. Focus the interview on the *delta* — what's new or changing — and skip questions the codebase already answers.

---

## Stage 1 — Interview

Ask in small conversational batches of 2–3 questions. Cover the following, adapting to what the user has already said:

**Problem space**
- What problem does this solve, and for whom?
- What's the current situation without this feature — what's broken, slow, or missing?
- Is this solving your own problem or someone else's?

**Desired outcome**
- What does success look like when this is shipped?
- How will you know it's working?
- Is there a simpler version of this that's still valuable?

**Scope & constraints**
- Are there things this should explicitly *not* do?
- Any existing patterns, systems, or decisions this needs to work within?
- Any hard constraints — time, platform, tech stack, API limits?

**Users & context**
- Who are the primary users, and what are they trying to accomplish?
- Are there secondary users or edge-case users worth naming?
- What context are they in when they use this (device, mental state, workflow)?

The interview is complete when every section of the PRD template below can be filled from the user's answers or the codebase, with nothing invented. Until then, keep asking.

---

## Stage 2 — Draft the PRD

Write the PRD in Markdown using this structure. Make every statement falsifiable or actionable: "search returns results in under 1 second for 10k records", not "search is performant".

```markdown
# PRD: [Cycle name]

**Status:** Draft  
**Author:** [User's name if known, otherwise omit]  
**Date:** [Today's date]  

---

## Problem statement

[2–4 sentences. What is broken or missing, for whom, and why it matters. Grounded in the interview.]

---

## Goals & success criteria

[Bulleted list. Each goal paired with how you'd measure or verify it. Example: "Users can complete X without Y friction — verified by task completion without error state."]

---

## User stories

[Format: As a [user type], I want to [action], so that [outcome]. Cover the primary flow and the most important edge cases. Aim for 4–8 stories.]

---

## Out of scope / non-goals

[Explicit list of things this version does not do. This prevents scope creep during implementation.]

---

## Constraints & risks

[Technical constraints, platform limitations, dependency risks, timeline pressures, or known unknowns. Be honest about what's uncertain.]

---

## Open questions

[Anything from the interview that remains unresolved. These may need answers before or during planning.]
```

Use `##` for section headers, `-` for bullets, and plain (unbolded) body text.

---

## Stage 3 — Refinement pass

Run this pass yourself, immediately after the draft, and revise the PRD inline:

- [ ] Every user story has a clear actor, action, and outcome
- [ ] Success criteria are specific enough to be verified
- [ ] Every qualifier (fast, simple, easy, good) is defined
- [ ] Every constraint names what it constrains and why
- [ ] Open questions are genuinely open — ones you could answer yourself are answered

Then show the revised PRD in full, followed by a short `## What changed in refinement` section: what you tightened or clarified, and why.

---

## Stage 4 — Save

In the same turn, write the revised PRD to **`CYCLE_DIR/1_ProductRequirementsDocument.md`**. The file holds the PRD sections only; `## What changed in refinement` stays in chat.

Then reply in plain prose:

- The path written.
- One short paragraph: edit that file if anything should change, then run `/devcycle_2_plan`.
