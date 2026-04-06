---
name: implement
description: Execute an approved implementation plan, then record what was done in 3_Implementation.md under .features/current/.
command: /implement
---

You are acting as an implementation agent. Your job is to carry out the approved plan: make the code and config changes, run tests as specified, and **persist a factual implementation summary** for `/review` and `/document`.

---

## Feature artifact root

After implementation is **complete** (or at a natural milestone the user approves), write:

**Path:** `[workspace-root]/.features/current/3_Implementation.md`

**Versioning:** If `3_Implementation.md` exists, use `3_Implementation_a.md`, then `_b`, `_c`, etc. (suffix before `.md`).

If **`.features/current/`** does not exist or is invalid, bootstrap like **`/ideate`**: create **`.features/`** if needed; **`DATE`** + **`SanitizedName`** → **`.features/DATE_SanitizedName/`** (with `_2`, `_3`, … if the basename is taken); from inside **`.features/`**, run **`ln -sfn DATE_SanitizedName current`**.

---

## Stage 1 — Gather inputs

- Load the **plan** from **`.features/current/2_Plan.md`** (or latest `2_Plan_*.md`) and/or conversation context.
- Load the **PRD** from **`1_ProductRequirementsDocument*.md`** in the same folder when needed.
- If the user skipped `/plan`, accept a short inline brief from chat and still record outcomes in `3_Implementation.md`.

---

## Stage 2 — Execute

- Follow the plan’s phases and tasks; match the project’s existing patterns.
- Run tests and builds the plan or user expects; record commands and results in the summary.
- Do not expand scope beyond the signed-off plan without explicit user approval.

---

## Stage 3 — Write `3_Implementation.md`

The summary is **for humans and `/document`**, not a second plan. Include:

- **What shipped** — bullets tied to plan phases
- **Files touched** — paths created/modified/deleted
- **Commands run** — e.g. `npm test`, `pytest`, commit hash if committed
- **Deviations** — where implementation differed from the plan and why
- **Notes for QA / review** — edge cases, manual checks, known gaps

Use Markdown with clear `##` headings. Save to the **next free** `3_Implementation*.md` filename.

---

## Stage 4 — Hand off

Confirm in one line:

> Implementation summary saved to **`.features/current/3_Implementation*.md`** — ready for `/review`.

---

## Rules

- Do not fabricate commits or test results — record what actually happened.
- If multiple implementation passes occur in one cycle, each pass gets the next **`_a` / `_b`** variant.
- **`0_Overview.md`** is never written here — that is **`/document`** only.
