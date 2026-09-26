---
name: devcycle_2_plan
description: Dev cycle step 2 — turn the PRD into a phased, refined implementation plan.
disable-model-invocation: true
---

You are acting as a senior technical lead. Translate the product requirement into a concrete, phased implementation plan that a developer or implementation agent can execute without ambiguity. Match the plan's detail to the work's complexity: include only the sections the work needs.

---

## Cycle folder

Read [`../devcycle_shared/CYCLE.md`](../devcycle_shared/CYCLE.md) and resolve **`CYCLE_DIR`** before anything else. It also defines artifact names and versioning.

---

## Stage 1 — Gather inputs

**PRD.** Use the PRD from the conversation, or the latest `1_ProductRequirementsDocument*.md` in `CYCLE_DIR` (or the one the user points to). If there is none, warn the user and proceed anyway:

> No PRD found. Implementation planning is stronger with one. Proceeding without it — run `/devcycle_1_ideate` first if you'd like one.

**Codebase map.** Use a codebase map from the conversation, or `CYCLE_DIR/_notes-[cycleName].md` if it exists.

**Direct description.** The user may invoke planning with a description (e.g. `/devcycle_2_plan add keyboard navigation to the dropdown`). Treat it as the input.

---

## Stage 2 — Clarify if needed

If the input leaves a critical technical question open — one that would force a meaningful assumption during planning — ask it before drafting, at most 2–3 targeted questions. Examples worth asking:

- "Should this work with the existing API or do we need a new endpoint?"
- "Is this replacing the current implementation or running alongside it?"

Decide naming, file locations, and code style yourself by following existing patterns. If nothing is critically unclear, go straight to Stage 3.

---

## Stage 3 — Draft the implementation plan

Write the plan in Markdown. The structure is adaptive: each section says when to include it, and sections with nothing meaningful to say are left out entirely.

```markdown
# Implementation plan: [Cycle name]

**Status:** Draft  
**PRD:** [Link or filename if available, otherwise "None"]  
**Date:** [Today's date]

---

## Overview

[2–3 sentences. What is being built, what approach is being taken, and what the end state looks like. Written for a developer reading this cold.]

---

## Architecture decisions

[Include when the work involves meaningful technical choices — data model design, component hierarchy, state management approach, API shape, integration strategy. Omit for straightforward additive work that follows existing patterns.]

[Format: Each decision as a heading with a brief rationale. Example:]

### State management
Using local component state rather than a shared store — the dropdown state is not needed outside the component and adding it to the store would introduce unnecessary coupling.

### API shape
Extending the existing `/users` endpoint with an optional `?include=permissions` query param rather than creating a new endpoint. Keeps the surface area small and avoids a breaking change.

---

## File & folder changes

[Include when files are being created, moved, renamed, or deleted. Omit when the work is purely additive to existing files with no structural changes.]

| Action | Path | Notes |
|--------|------|-------|
| Create | `src/components/dropdown/Dropdown.ts` | Main component |
| Create | `src/components/dropdown/Dropdown.types.ts` | Prop types |
| Create | `src/components/dropdown/index.ts` | Public export |
| Modify | `src/components/index.ts` | Add dropdown export |

---

## Component & interface design

[Include when new components, classes, or public interfaces are being defined. Omit for internal refactors or config changes. Show the shape — types, props, key methods — not the full implementation.]

---

## API & data model

[Include when the work touches data structures, API contracts, database schema, or external service integration. Omit otherwise.]

---

## Phased task sequence

[Always include. Organize tasks into phases. Each phase should be independently completable and leave the codebase in a working state. Dependencies between tasks should be explicit.]

### Phase 1 — [Name, e.g. Foundation]
[What this phase establishes and why it comes first.]

- [ ] Task description — [S/M/L] — [any dependency noted inline]
- [ ] Task description — [S/M/L]

### Phase 2 — [Name, e.g. Feature]
[What this phase builds on top of Phase 1.]

- [ ] Task description — [S/M/L]
- [ ] Task description — [S/M/L] — depends on: task above

### Phase 3 — [Name, e.g. Polish]
[Refinements, edge cases, error states, accessibility, documentation.]

- [ ] Task description — [S/M/L]

**Complexity key:** S = under an hour / M = half day / L = full day or more

---

## Risk flags & open technical questions

[Include when there are meaningful uncertainties, integration risks, performance concerns, or decisions that need validation during implementation. Omit if there are none.]

- **[Risk name]:** [What could go wrong and what to watch for]
- **[Open question]:** [What needs to be decided or validated during implementation]

---

## Out of scope

[Include when it's important to be explicit about what this plan does not cover — especially if the PRD had non-goals that could bleed into implementation. Omit if obvious.]
```

---

## Stage 4 — Refinement pass

Run this pass yourself, immediately after drafting, and revise the plan inline:

**PRD alignment**
- Every PRD goal has at least one corresponding task.
- Every PRD constraint and risk is addressed.
- Nothing in the plan contradicts the PRD's out-of-scope section.

**Task clarity**
- Every task is specific enough that a developer knows exactly what done looks like.
- Bundled tasks are split into one task each.
- Dependencies between tasks are explicit.

**Complexity**
- Each S/M/L estimate is honest.
- L tasks that can be broken down are broken down.
- The total scope is proportionate to the problem.

**Deferral candidates**
- Tag nice-to-have tasks inline with `[deferrable]` and keep them in the plan: `- [ ] Animate open/close transition — S — [deferrable]`

Then show the revised plan in full, followed by a short `## What changed in refinement` section: what you tightened, split, flagged, or removed.

---

## Stage 5 — Save

In the same turn, write the revised plan to **`CYCLE_DIR/2_Plan.md`**. The file holds the plan sections only; `## What changed in refinement` stays in chat.

Then reply in plain prose:

- The path written.
- **ADR candidates:** architecture decisions that meet all three criteria in [`../domain-modeling/ADR-FORMAT.md`](../domain-modeling/ADR-FORMAT.md) (hard to reverse, surprising without context, a real trade-off). Offer to record each as an ADR in `.docs/adr/`, since the plan itself is deleted when the cycle closes. Omit this line when there are none.
- One short paragraph: edit that file if anything should change (phases, estimates, deferrables), then run `/devcycle_3_implement`.
