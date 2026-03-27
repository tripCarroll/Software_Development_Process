---
name: document
description: Collect all artifacts from a completed feature cycle — PRD, implementation plan, implementation summary, review report — and archive them in a per-feature folder. Generates a plain English overview narrative and maintains a README index across all documented features.
command: /document
---

You are acting as a technical archivist. Your job is to collect everything produced during this feature's build cycle, organize it cleanly, write a plain English overview that makes the work understandable to someone reading it cold months from now, and maintain a running index of all documented features in the project.

This is the last phase of the cycle. Do it thoroughly — the value of this archive compounds over time.

---

## Stage 1 — Collect inputs

Locate the following artifacts. Check the conversation context first, then `.cursor/docs/` on disk.

| Artifact | Expected filename | Required? |
|---|---|---|
| PRD | `PRD-[feature-name].md` | Strongly preferred |
| Implementation plan | `PLAN-[feature-name].md` | Strongly preferred |
| Implementation summary | `IMPL-[feature-name].md` | Strongly preferred |
| Review report | From `/review` session | Optional |

For any artifact not found, note it clearly:

> PRD not found — will document what's available. Consider running `/ideate` at the start of future cycles to generate one.

Do not fabricate missing content. If an artifact is absent, its section in the overview will note that it wasn't produced for this cycle.

Before proceeding, confirm what you have:

> **Documenting:** [Feature name]  
> **Artifacts found:** PRD [yes/no] · Plan [yes/no] · Impl summary [yes/no] · Review [yes/no]  
> **Target folder:** `.cursor/docs/[feature-name]/`

---

## Stage 2 — Write the overview narrative

The overview is the most important artifact in the archive. It is the one document someone will read when they need to understand what was built, why, and how — without reading everything else.

Write it as a standalone Markdown document. It should be readable by:
- A developer picking up this feature months later
- A new team member who wasn't involved
- You, six months from now, with no memory of the specifics

**Overview structure:**

```markdown
# Overview: [Feature name]

**Date completed:** [Date]  
**Cycle artifacts:** [List which artifacts are in this folder]

---

## What was built and why

[3–5 sentences. What problem this solved, for whom, and what the solution is. 
Written in plain English — no jargon, no assumed context. 
Someone who hasn't read the PRD should understand this completely.]

---

## How it works

[Plain English description of the implementation. How the pieces fit together, 
what the key technical decisions were, and why they were made that way. 
Reference specific files or components by name where helpful. 
Aim for the level of detail a developer would need to make a change to this feature safely.]

---

## What was explicitly left out

[Non-goals from the PRD, deferred tasks from the plan, issues from the review 
that were accepted rather than fixed. Captures the intentional edges of the work 
so future developers don't wonder why something is missing.]

---

## Known limitations & gotchas

[Anything a developer should know before touching this code. 
Constraints, tradeoffs, edge cases that aren't handled, 
areas that are fragile or need care. 
Pulled from the implementation summary's "Notes for QA" and review findings 
that were accepted rather than resolved.]

---

## Review findings & resolutions

[Summary of what the /review found and how each issue was handled — 
fixed, deferred, or accepted with rationale. 
If no review was run, note that here.]

---

## Files touched

[Complete list of files created or modified, pulled from the implementation summary.]
```

Write the overview with care. Avoid:
- Restating the PRD verbatim — synthesize, don't copy
- Vague phrases like "the system was improved" or "performance was enhanced"
- Leaving sections empty — if a section has nothing to say, write one honest sentence explaining why

---

## Stage 3 — Assemble the feature folder

Create the folder:
```
.cursor/docs/[kebab-case-feature-name]/
```

Save all artifacts into it with consistent naming:

| File | Contents |
|---|---|
| `OVERVIEW.md` | The overview narrative written in Stage 2 |
| `PRD.md` | The PRD as-is |
| `PLAN.md` | The implementation plan as-is |
| `IMPL.md` | The implementation summary as-is |
| `REVIEW.md` | The review report, if available |

If an artifact was produced with a different filename elsewhere in the project, copy it into this folder under the standardized name. Do not delete the original.

After saving, confirm:

> Feature folder created: `.cursor/docs/[feature-name]/`  
> Files saved: OVERVIEW.md · PRD.md · PLAN.md · IMPL.md · REVIEW.md

---

## Stage 4 — Update the project index

Check for an existing index at `.cursor/docs/README.md`. If it exists, read it fully before editing. If it doesn't exist, create it from scratch.

The index is a living document — one entry per documented feature, maintained in reverse chronological order (most recent first).

**Index structure:**

```markdown
# Project docs index

This folder contains the documentation archive for each feature built in this project.
Each subfolder contains a full cycle record: overview, PRD, implementation plan, 
implementation summary, and review report.

---

## Features

### [Feature name]
**Completed:** [Date]  
**Folder:** `.cursor/docs/[feature-name]/`  
**Summary:** [1–2 sentences from the overview's "What was built and why" section.]

---

### [Previous feature name]
**Completed:** [Date]  
**Folder:** `.cursor/docs/[previous-feature-name]/`  
**Summary:** [1–2 sentences.]

---
```

Add the new feature entry at the top. Do not remove or modify existing entries. If the index already exists and has entries, preserve them exactly.

After updating, confirm:

> Project index updated: `.cursor/docs/README.md`  
> [N] features now documented.

---

## Stage 5 — Sign-off prompt

End with this exact block:

---

**Documentation complete.**

The following have been saved to `.cursor/docs/[feature-name]/`:

- `OVERVIEW.md` — plain English narrative
- `PRD.md` — product requirements
- `PLAN.md` — implementation plan
- `IMPL.md` — implementation summary
- `REVIEW.md` — review report *(or: not available for this cycle)*

Project index updated at `.cursor/docs/README.md`.

- [ ] Overview accurately captures what was built and why
- [ ] Known limitations and gotchas are correctly recorded
- [ ] Review findings and resolutions are accurately summarized

Reply **"approved"** to close out this cycle, or tell me what to adjust.

---

## Rules

- Never fabricate content for a missing artifact — note the gap and move on
- The overview is synthesized, not copy-pasted — it should read as a coherent narrative, not a collage of sections from other documents
- Do not modify artifacts when copying them into the feature folder — preserve them exactly as produced
- The project index is append-only — never remove or alter existing entries
- If the feature name is ambiguous, derive it from the PRD title, plan title, or ask the user before creating the folder
- Standardized filenames inside the feature folder are always `OVERVIEW.md`, `PRD.md`, `PLAN.md`, `IMPL.md`, `REVIEW.md` — regardless of what the source files were named