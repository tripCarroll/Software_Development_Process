---
name: SoftwareCycle_5_document
description: Closes a feature cycle—resolves or creates .docs/features/YYYY-MM-DD_FeatureName/, writes 0_Overview.md, ensures artifacts 1–4 exist, updates .docs/features/README.md. Use when the user runs /SoftwareCycle_5_document (Cursor), archives a cycle, or consolidates feature artifacts (any host). Does not author 1_ProductRequirementsDocument through 4_Review.
---

## Portable usage (Cursor & Claude)

**`/SoftwareCycle_5_document`** = Cursor shorthand for this archiving workflow; same when the user asks to “close the cycle”, “write the overview”, or “archive `.docs/features`.” **`[workspace-root]`** is the repository or project workspace root.

---

You are acting as a technical archivist. **`0_Overview.md` is produced only by this skill.** Other artifacts (`1_ProductRequirementsDocument.md` … `4_Review.md`) should already exist from earlier skills; this skill **resolves the feature folder**, **ensures** numbered files are there, fills gaps from conversation context when possible, and writes the overview.

**Never** create or use **`.docs/features/current/`**. All artifacts live directly in the dated feature folder.

---

## Project folder pattern

**Durable folder basename:** `YYYY-MM-DD_FeatureTitle`  
**Full path:** `[workspace-root]/.docs/features/YYYY-MM-DD_FeatureTitle/`

**Date** and **sanitization** rules:

- **`DATE`:** `YYYY-MM-DD` (prefer the session **“today”** from user context when available).
- **`SanitizedName`:** from the feature / project name — allow letters, numbers, hyphen, underscore; replace spaces with `_`; strip or replace other characters; collapse repeats; **avoid empty string** (fallback `feature`).
- **Folder basename:** `DATE_SanitizedName` (e.g. `2026-03-28_OAuthRefresh`).
- If `.docs/features/DATE_SanitizedName` already exists and you must create a new distinct folder, append `_2`, `_3`, … until unused.

**Path while executing:** Use the resolved **`TARGET_DIR`** below for all reads/writes. Each cycle uses one dated folder — there is no separate `current` staging area.

---

## Canonical filenames (under `TARGET_DIR`)

| File | Producing skill |
|------|-----------------|
| `0_Overview.md` | **This skill only** — plain-English narrative of the cycle |
| `1_ProductRequirementsDocument.md` | **SoftwareCycle_1_ideate** |
| `2_Plan.md` | **SoftwareCycle_2_plan** |
| `3_Implementation.md` | **SoftwareCycle_3_implement** (variants: `3_Implementation_a.md`, …) |
| `4_Review.md` | **SoftwareCycle_4_review** (variants: `4_Review_a.md`, …) |

**Versioning:** When writing **`0_Overview.md`**, if it already exists in `TARGET_DIR`, use the next free name: `0_Overview_a.md`, `0_Overview_b.md`, etc. (suffix **before** `.md`). Same pattern for any file this skill **creates** when a base name collides.

For artifacts **1–4**, versioning is owned by their producing skills; this skill does not rename them except when backfilling with the next free variant.

---

## Stage 0 — Resolve or create the feature folder (`TARGET_DIR`)

1. Ensure **`.docs/features/`** exists (create if missing). Workspace root = repository or project workspace root.

2. **Search** for the feature folder (same rules as **SoftwareCycle_1_ideate**):
   - If the user or conversation names a folder matching **`^\d{4}-\d{2}-\d{2}_.+`**, set **`TARGET_DIR`** — stop.
   - Else list dated subdirectories: if **exactly one** exists, use it; if **multiple** exist, prefer the most recently modified folder lacking **`0_Overview.md`**, or **ask the user** if ambiguous.
   - **Legacy:** If **`.docs/features/current`** is a symlink to a dated folder, use that folder as **`TARGET_DIR`** for this cycle.

3. **If `TARGET_DIR` is not set:**
   - **Ask the user:** *What would you like to name this project?* (short label is fine; it becomes `DATE_SanitizedName` after the date prefix.)
   - Unless they already gave an explicit name in the same message — then use that.
   - Compute **`DATE`**, sanitize to **`SanitizedName`**, set **`TARGET_DIR`** = `.docs/features/DATE_SanitizedName` (with `_2`, `_3`, … disambiguation if that path already exists).
   - **`mkdir -p TARGET_DIR`**. Do **not** create a `current` symlink.

4. **Optional `README.md` in `TARGET_DIR`:** If `TARGET_DIR` was **just created** and has no `README.md`, add one: line 1 human feature name, line 2 **Started:** `DATE`, line 3 note that numbered artifacts and `0_Overview.md` live here.

5. **Legacy cleanup:** If **`.docs/features/current`** is a symlink whose target is **`TARGET_DIR`**, delete the symlink after artifacts are verified (e.g. `rm .docs/features/current`). Do not delete folder contents.

---

## Stage 1 — Collect inputs

1. Open **`TARGET_DIR`**.
2. Check which of these exist (including suffixed variants `*_a.md`, `*_b.md`, …):
   - `1_ProductRequirementsDocument*.md`
   - `2_Plan*.md`
   - `3_Implementation*.md`
   - `4_Review*.md`
3. Pull missing content from **conversation context** or legacy paths only when it was actually produced this cycle — **do not fabricate**.

Before proceeding, confirm:

> **Documenting:** [Feature name]  
> **Feature folder:** `.docs/features/<DATE>_<SanitizedName>/`  
> **Artifacts found:** 1_PRD [yes/no] · 2_Plan [yes/no] · 3_Implementation [yes/no] · 4_Review [yes/no]

For any missing file, note it clearly in the overview and in the sign-off.

---

## Stage 3 — Write `0_Overview.md` only

This is the only numbered artifact **authored** here. Use the structure below. Synthesize from PRD, plan, implementation notes, and review — do not paste them verbatim.

```markdown
# Overview: [Feature name]

**Date completed:** [Date]  
**Cycle artifacts:** List files in the feature folder for this cycle (0–4 as applicable)

---

## What was built and why

[3–5 sentences. Plain English. Someone who has not read the PRD should understand.]

---

## How it works

[How the pieces fit together, key technical decisions, pointers to real paths in the repo.]

---

## What was explicitly left out

[Non-goals, deferred plan items, review items accepted without fix.]

---

## Known limitations & gotchas

[Constraints, fragile areas, operational notes.]

---

## Review findings & resolutions

[What the SoftwareCycle_4_review skill found; fixed, deferred, or accepted — or state that no review ran.]

---

## Files touched

[From implementation summary / memory of the cycle.]
```

Save to **`TARGET_DIR`** using the **next free** `0_Overview*.md` name per the versioning rule.

---

## Stage 4 — Ensure artifacts 1–4 are inside `TARGET_DIR`

- If **`1_ProductRequirementsDocument.md`** (or a versioned variant) is missing but exists elsewhere or only in chat, **write** it into `TARGET_DIR` using the **next free** filename for that artifact type (`1_ProductRequirementsDocument_a.md`, etc., if the base name is taken).
- Repeat logic for **2**, **3**, **4** only when you have genuine content to persist — do not invent.
- If a producing skill already saved the correct file in `TARGET_DIR`, **do not duplicate** — leave as-is.
- **Do not delete** originals in other folders if you **copy** from them (legacy `.cursor/docs/` copies may remain until manually cleaned).

After saving, confirm:

> **`0_Overview*.md`** written under **`<TARGET_DIR>`**  
> Artifacts **1–4** verified or backfilled where possible.

---

## Stage 5 — Update the project index

Maintain **`.docs/features/README.md`** at the workspace root (create if missing).

The index lists feature cycle folders (`YYYY-MM-DD_Name`). A cycle is **in progress** when its folder exists but lacks **`0_Overview.md`**; **completed** once **`0_Overview.md`** is written.

**Structure:**

```markdown
# Feature cycles index

---

## Feature cycles

### YYYY-MM-DD_FeatureSlug
**Folder:** `.docs/features/YYYY-MM-DD_FeatureSlug/`  
**Status:** In progress | Completed  
**Summary:** [1–2 sentences when completed]

---
```

**Append** or **update** an entry for this cycle’s **`TARGET_DIR`** when documentation is complete (summary from `0_Overview` or one line). **Append-only** for past entries — do not remove older rows unless correcting a clear mistake.

If **`.docs/README.md`** still exists from legacy docs, you may add a one-line pointer to `.docs/features/README.md` rather than duplicating content.

---

## Stage 6 — Sign-off prompt

End with:

---

**Documentation complete.**

The following have been saved under **`.docs/features/<DATE>_<SanitizedName>/`**:

- **`0_Overview*.md`** — plain English narrative (**this skill only**)
- **`1_ProductRequirementsDocument*.md`** — present or noted missing
- **`2_Plan*.md`** — present or noted missing
- **`3_Implementation*.md`** — present or noted missing
- **`4_Review*.md`** — present or noted missing

Index updated at **`.docs/features/README.md`** (or noted if skipped).

- [ ] Overview accurately captures what was built and why  
- [ ] Known limitations and gotchas are correctly recorded  
- [ ] Review findings and resolutions are accurately summarized  

Reply **"approved"** to close out this cycle, or tell me what to adjust.

---

## Rules

- **`0_Overview`** is **only** produced by **SoftwareCycle_5_document** (e.g. `/SoftwareCycle_5_document` in Cursor).
- Never fabricate missing PRD/plan/implementation/review text — note gaps in the overview and sign-off.
- **Always** run Stage 0 so the cycle has a **dated feature folder** and artifacts live **inside** it. **Never** create **`.docs/features/current/`**.
- **Legacy:** Remove an existing **`current`** symlink after consolidating into **`TARGET_DIR`** (Stage 0 step 5).
- Prefer **next-free** version suffixes `_a`, `_b`, `_c` **before** `.md`.
