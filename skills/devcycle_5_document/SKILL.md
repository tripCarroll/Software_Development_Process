---
name: devcycle_5_document
description: Dev cycle step 5 — condense the cycle folder into one summary file, update the changelog, and remove the folder with permission.
disable-model-invocation: true
---

You are acting as a technical archivist. This skill turns a finished cycle's working folder into its **cycle summary**: one file, `[date]-[cycleName].md`, written beside the folder in the cycles directory. Once the user approves, the folder is removed and the summary is the only record of the cycle.

---

## Stage 0 — Cycle folder

Read [`../devcycle_shared/CYCLE.md`](../devcycle_shared/CYCLE.md) and resolve **`CYCLE_DIR`** before anything else. It also lists every artifact and which skill wrote it.

The **cycle summary** path is `CYCLE_DIR` with `.md` appended: for `cycles/2026-08-20-button-refactor/`, it is `cycles/2026-08-20-button-refactor.md`.

---

## Stage 1 — Collect inputs

1. Read every file in **`CYCLE_DIR`**, including any not listed in `CYCLE.md`.
2. Pull content that exists only in the conversation when it was actually produced this cycle. Everything in the summary traces to a real artifact or conversation.

Before proceeding, confirm:

> **Documenting:** [Cycle name]  
> **Cycle folder:** `<CYCLE_DIR>`  
> **Artifacts found:** Notes [yes/no] · PRD [yes/no] · Plan [yes/no] · Implementation [yes/no] · Review [yes/no]

For any missing artifact, note it in the summary and in the sign-off.

---

## Stage 2 — Write the cycle summary

The folder is removed after this skill runs, so the summary is the permanent record. Synthesize from every input rather than pasting, and carry forward every decision, deviation from plan, deferred item, and review finding a future reader would need. Use this structure:

```markdown
# [date] [Cycle name]

**Started:** [date from the folder name]  
**Completed:** [today]  
**Artifacts summarized:** [which inputs existed]

---

## What was built and why

[3–5 sentences. Plain English. Someone who never saw the PRD should understand.]

---

## How it works

[How the pieces fit together, key technical decisions and their rationale, pointers to real paths in the repo. For decisions recorded as ADRs in `.docs/adr/`, link the ADR in place of restating it.]

---

## Deviations from plan

[Where implementation differed from the plan and why. Omit if none.]

---

## What was explicitly left out

[Non-goals, deferred plan items, review items accepted without fix.]

---

## Known limitations & gotchas

[Constraints, fragile areas, operational notes.]

---

## Review findings & resolutions

[What devcycle_4_review found; fixed, deferred, or accepted — or state that no review ran.]

---

## Files touched

[From the implementation summary.]
```

Save it to the cycle summary path from Stage 0.

---

## Stage 3 — Update the changelog

Add an entry for this cycle to its changelog:

- **With a project:** under `## Changelog` in `DOCS_ROOT/projects/[projectTitle]/[projectTitle].md`. If the file is missing, create it with a `# [projectTitle]` title and a `## Changelog` heading.
- **Without a project:** in `DOCS_ROOT/cycles/README.md`. If the file is missing, create it with a `# Dev cycles` title.

**Entry format** (link path relative to the changelog file; drop the `cycles/` prefix in `DOCS_ROOT/cycles/README.md`):

```markdown
### [Cycle name] | *[Date completed]*
[1–2 sentence summary.]
[Link to cycle](cycles/[date]-[cycleName].md)
```

Add new entries below existing ones; edit older entries only to correct a clear mistake.

**Shipped features.** With a project, check its `## Planned features` against what this cycle built. Move each one the cycle shipped to `## Features`, rewritten as what it does today and ending with its main code path in backticks. Remove `## Planned features` once it is empty.

---

## Stage 4 — Sign-off and folder removal

End with:

---

**Cycle summary written:** `<cycle summary path>`  
**Changelog updated:** `<[projectTitle].md or DOCS_ROOT/cycles/README.md>`  
**Features moved from Planned:** [list, or "none"]  
**Missing artifacts:** [list, or "none"]

- [ ] Summary accurately captures what was built and why  
- [ ] Deviations, deferrals, and limitations are correctly recorded  
- [ ] Review findings and resolutions are accurately summarized  

Once the summary looks right, I'll remove **`<CYCLE_DIR>`** and these files:

- [every file in `CYCLE_DIR`]

The summary will then be the only record of this cycle. Reply **"remove"** to delete the folder, or tell me what to adjust in the summary first.

---

Remove `CYCLE_DIR` only after the user explicitly approves removal in reply to this prompt. If they ask for changes, revise the summary and ask again. If they decline, leave the folder in place and say so.
