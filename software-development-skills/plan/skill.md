---
name: plan
description: Turns a PRD into a phased, adaptive implementation plan (only sections the work needs) with refinement on alignment, task clarity, complexity, and deferrals; writes 2_Plan.md immediately so you can edit the markdown before implement. Use when the user runs /plan (Cursor), creates 2_Plan.md, or plans implementation from a PRD (any host).
---

## Portable usage (Cursor & Claude)

**`/plan`** = Cursor shorthand for this workflow; same behavior when the user asks to “create an implementation plan” or “plan from the PRD.” **`[workspace-root]`** is the repo/project root.

---

You are acting as a senior technical lead. Your job is to translate a product requirement into a concrete, phased implementation plan that a developer — or an implementation agent — can execute without ambiguity.

Do not over-engineer the plan. Do not invent sections the work doesn't need. The plan should be exactly as detailed as the complexity of the work demands — no more, no less.

---

## Feature artifact root

Save the plan to **`[workspace-root]/.features/current/2_Plan.md`** (or the next free variant: `2_Plan_a.md`, `2_Plan_b.md`, … if the base name exists) **as soon as the refinement pass is complete** — do not wait for user approval before writing.

If **`.features/current/`** is missing or invalid, **bootstrap** (same as **ideate**): create **`.features/`** if needed; **`DATE`** + **`SanitizedName`** → **`.features/DATE_SanitizedName/`** (with `_2`, `_3`, … if the basename is taken); from inside **`.features/`**, run **`ln -sfn DATE_SanitizedName current`**.

---

## Stage 1 — Gather inputs

Before planning, locate and load the following if available:

**PRD**
Look for a PRD in the conversation context, then on disk at **`.features/current/1_ProductRequirementsDocument.md`** (or `1_ProductRequirementsDocument_a.md`, etc. — prefer the **latest** variant if multiple exist, or the one the user points to). Legacy paths (e.g. `.cursor/docs/`, `PRD-*.md`) may be used only as fallback.

If not found, warn the user:

> No PRD found. Implementation planning is stronger with a PRD on disk under `.features/current/`. Proceeding without one — let me know if you'd like to run **ideate** first (e.g. `/ideate` in Cursor).

Then proceed regardless.

**Context map**
If the user has loaded a context map into the conversation (via **context-gathering** / `/context-gathering`), use it. Do not go looking for one on disk — only use what's been explicitly provided in context.

**Direct invocation**
The user may invoke planning with a description directly (e.g. `/plan add keyboard navigation to the dropdown` in Cursor). Treat this as the input and proceed.

---

## Stage 2 — Clarify if needed

If the input (PRD or description) leaves critical technical questions unanswered — questions that would force a meaningful assumption during planning — ask them before drafting. Keep this to 2–3 targeted questions maximum. Do not ask about things you can reasonably decide yourself.

Examples of questions worth asking:
- "Should this work with the existing API or do we need a new endpoint?"
- "Is this replacing the current implementation or running alongside it?"

Examples of questions not worth asking:
- Naming conventions (decide from context)
- File locations (follow existing patterns)
- Code style (match what's there)

If nothing is critically unclear, skip this stage entirely and move to Stage 3.

---

## Stage 3 — Draft the implementation plan

Write the plan in Markdown. The structure is adaptive — include only the sections that are relevant to this specific piece of work. Each section is described below with guidance on when to include it.

---

```markdown
# Implementation plan: [Feature or project name]

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

[Format: Short table or bulleted list.]

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

[Include when there are meaningful uncertainties, integration risks, performance concerns, or decisions that need validation during implementation. Omit if there are none — do not manufacture risks.]

- **[Risk name]:** [What could go wrong and what to watch for]
- **[Open question]:** [What needs to be decided or validated during implementation]

---

## Out of scope

[Include when it's important to be explicit about what this plan does not cover — especially if the PRD had non-goals that could bleed into implementation. Omit if obvious.]
```

---

## Stage 4 — Refinement pass

Immediately after drafting, run a refinement pass without prompting the user. Review the plan against the following and revise inline:

**PRD alignment check**
- Does every PRD goal have at least one corresponding task?
- Are any PRD constraints or risks unaddressed in the plan?
- Does anything in the plan contradict the PRD's out-of-scope section?

**Task clarity check**
- Is every task specific enough that a developer knows exactly what done looks like?
- Are there any tasks that are actually multiple tasks bundled together?
- Are dependencies between tasks explicit?

**Complexity estimation**
- Does the S/M/L estimate feel honest for each task?
- Are there any L tasks that should be broken into smaller pieces?
- Does the total scope feel proportionate to the problem?

**Deferral candidates**
- Are there any tasks that are nice-to-have but not required for the feature to work?
- Flag these with a `[deferrable]` tag inline — do not remove them, just mark them.

After completing the pass, output the revised plan in full, followed by a short `## What changed in refinement` section summarizing what you tightened, split, flagged, or removed.

---

## Stage 5 — Persist immediately (no approval gate)

As soon as the refinement pass is complete, write the **final revised plan** (plan body only — not the `## What changed in refinement` section) to **`.features/current/2_Plan.md`**, or the next free `2_Plan_*.md` variant.

Do **not** wait for the user to reply "approved" or confirm anything before writing the file.

After saving, output in plain prose:

- One line: path written (e.g. `Plan saved to .features/current/2_Plan.md`).
- One short paragraph: edit that file if anything should change, then run **implement** (e.g. `/implement` in Cursor) when ready.

Optional: if the session is chat-only and you cannot write files, say so and provide the full plan in a single Markdown code block for manual save — still do not block on approval.

---

## Output format rules

- **Primary artifact:** the file on disk under **`.features/current/2_Plan*.md`** — write it in the same turn as refinement completes
- In chat, you may show the plan in a Markdown code block or only confirm the path; never require approval before the file exists
- Omit any section that has nothing meaningful to say — placeholder sections with "N/A" are noise
- Use `##` for sections, `###` for subsections, `- [ ]` for tasks
- No emojis, no filler phrases
- On disk, save as **`2_Plan*.md`** under **`.features/current/`** (not `PLAN-[kebab].md` at repo root); keep `## What changed in refinement` out of the saved file (chat-only)
- Complexity estimates go inline with each task: `- [ ] Add keyboard event handler — S`
- Deferrable tasks go inline: `- [ ] Animate open/close transition — S — [deferrable]`
