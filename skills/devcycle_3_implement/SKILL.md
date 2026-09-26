---
name: devcycle_3_implement
description: Dev cycle step 3 — execute the plan, run its tests, and record what shipped.
disable-model-invocation: true
---

You are acting as an implementation agent. Carry out the plan — code and config changes, tests, builds — and record a factual implementation summary for **devcycle_4_review** and **devcycle_5_document**.

---

## Cycle folder

Read [`../devcycle_shared/CYCLE.md`](../devcycle_shared/CYCLE.md) and resolve **`CYCLE_DIR`** before anything else. It also defines artifact names and versioning.

---

## Stage 1 — Gather inputs

- **Plan:** the latest `2_Plan*.md` in `CYCLE_DIR`, or the plan in the conversation. If the user skipped planning, use a short brief from chat.
- **PRD:** the latest `1_ProductRequirementsDocument*.md`, when a task needs the requirement behind it.
- **Codebase map:** `CYCLE_DIR/_notes-[cycleName].md`, if it exists, for the patterns to follow.

---

## Stage 2 — Execute

- Work through the plan's phases in order, matching the project's existing patterns.
- Stay within the plan. When something outside it seems necessary, ask the user before doing it.
- Run the tests and builds the plan or user calls for, and keep the commands and results for the summary.
- Leave `2_Plan.md` as written; progress is recorded only in the implementation summary.

Execution is complete when every plan task is either done or listed under **Deviations** with the reason, and every test and build the plan calls for has been run.

---

## Stage 3 — Write the implementation summary

Write **`CYCLE_DIR/3_Implementation.md`** with these `##` sections. Record only what actually happened: real commands, real results, real commit hashes.

- **What shipped** — bullets tied to plan phases
- **Files touched** — paths created, modified, or deleted
- **Commands run** — e.g. `npm test`, `pytest`, with results; commit hash if committed
- **Deviations** — where implementation differed from the plan, and why
- **Notes for review** — edge cases, manual checks, known gaps

---

## Stage 4 — Hand off

Reply in one line:

> Implementation summary saved to `<path>` — ready for `/devcycle_4_review` (best run in a fresh chat).
