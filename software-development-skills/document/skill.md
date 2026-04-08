---
name: document
description: Closes a feature cycle—resolves or creates .features/YYYY-MM-DD_FeatureName/, moves artifacts, removes .features/current (symlink or staging dir), writes 0_Overview.md, ensures artifacts 1–4 exist, updates .features/README.md. Use when the user runs /document (Cursor), archives a cycle, or consolidates feature artifacts (any host). Does not author 1_ProductRequirementsDocument through 4_Review.
---

## Portable usage (Cursor & Claude)

**`/document`** = Cursor shorthand for this archiving workflow; same when the user asks to “close the cycle”, “write the overview”, or “archive `.features`.” **`[workspace-root]`** is the repository or project workspace root.

---

You are acting as a technical archivist. **`0_Overview.md` is produced only by this skill.** Other artifacts (`1_ProductRequirementsDocument.md` … `4_Review.md`) should already exist from earlier skills; this skill **resolves the durable project folder**, **moves** in-progress artifacts into it when needed, **removes `.features/current`** after consolidation, **ensures** numbered files are there, fills gaps from conversation context when possible, and writes the overview.

---

## Project folder pattern

**Durable folder basename:** `YYYY-MM-DD_FeatureTitle`  
**Full path:** `[workspace-root]/.features/YYYY-MM-DD_FeatureTitle/`

**Date** and **sanitization** rules:

- **`DATE`:** `YYYY-MM-DD` (prefer the session **“today”** from user context when available).
- **`SanitizedName`:** from the feature / project name — allow letters, numbers, hyphen, underscore; replace spaces with `_`; strip or replace other characters; collapse repeats; **avoid empty string** (fallback `feature`).
- **Folder basename:** `DATE_SanitizedName` (e.g. `2026-03-28_OAuthRefresh`).
- If `.features/DATE_SanitizedName` already exists and you must create a new distinct folder, append `_2`, `_3`, … until unused.

**Path while executing:** Use the resolved **`TARGET_DIR`** below for all reads/writes and moves. **After this skill finishes, `.features/current` must not remain** — it is removed once artifacts live in `TARGET_DIR` (see Stage 1). The next cycle recreates `current` via **ideate** or the bootstrap steps in other pipeline skills.

---

## Canonical filenames (under `TARGET_DIR`)

| File | Producing skill |
|------|-----------------|
| `0_Overview.md` | **This skill only** — plain-English narrative of the cycle |
| `1_ProductRequirementsDocument.md` | **ideate** |
| `2_Plan.md` | **plan** |
| `3_Implementation.md` | **implement** (variants: `3_Implementation_a.md`, …) |
| `4_Review.md` | **review** (variants: `4_Review_a.md`, …) |

**Versioning:** When writing **`0_Overview.md`**, if it already exists in `TARGET_DIR`, use the next free name: `0_Overview_a.md`, `0_Overview_b.md`, etc. (suffix **before** `.md`). Same pattern for any file this skill **creates** when a base name collides.

For artifacts **1–4**, versioning is owned by their producing skills; this skill does not rename them except when consolidating into `TARGET_DIR` with the next free variant.

---

## Stage 0 — Resolve or create the project folder (`TARGET_DIR`)

1. Ensure **`.features/`** exists (create if missing). Workspace root = repository or project workspace root.

2. **Search** for an existing project folder:
   - If **`.features/current`** is a **symbolic link**, resolve it (real path). If the target is a **directory** under `.features/` whose basename matches **`^\d{4}-\d{2}-\d{2}_.+`**, set **`TARGET_DIR`** to that directory and **stop** Stage 0 (folder already exists).
   - Else, **list** subdirectories of `.features/` whose names match **`^\d{4}-\d{2}-\d{2}_.+`**. If the user or conversation clearly names one matching folder (same slug), you may set **`TARGET_DIR`** to it. If **none** match or **multiple** could apply and the user has not confirmed, **do not guess** — continue to step 3.

3. **If `TARGET_DIR` is not set** (no matching folder found or ambiguous):
   - **Ask the user:** *What would you like to name this project?* (short label is fine; it becomes `DATE_SanitizedName` after the date prefix.)
   - Unless they already gave an explicit name in the same message — then use that.
   - Compute **`DATE`**, sanitize to **`SanitizedName`**, set **`TARGET_DIR`** = `.features/DATE_SanitizedName` (with `_2`, `_3`, … disambiguation if that path already exists).
   - **Create** `TARGET_DIR` as a directory.

4. **Optional `README.md` in `TARGET_DIR`:** If `TARGET_DIR` was **just created** and has no `README.md`, add one: line 1 human feature name, line 2 **Started:** `DATE`, line 3 note that numbered artifacts and `0_Overview.md` live here and that **document** consolidated the cycle.

---

## Stage 1 — Move all artifacts into `TARGET_DIR`

**Goal:** Every file for this cycle that belongs in the numbered set (and any `README.md` from the cycle) should live **inside** `TARGET_DIR`. When done, **remove `.features/current`** so the archived cycle exists only under the dated folder.

1. **If `.features/current` is a symlink** whose target **is** `TARGET_DIR` (same resolved path): artifacts are already in the right place; **skip** moving (only verify in later stages).

2. **If `.features/current` is a plain directory** (not a symlink) **and** its resolved path is **not** the same as `TARGET_DIR`:
   - **Move** (not copy) into `TARGET_DIR` every relevant file: `0_*.md` through `4_*.md`, `README.md` if present, and any other obvious cycle artifacts in that directory. **Do not** move unrelated dotfiles or non-cycle content without user confirmation.

3. **If `current` did not exist** and artifacts only exist under **`TARGET_DIR`** after creation: nothing to move from `current`.

4. **If** numbered artifacts exist **only** under another path (e.g. legacy `.cursor/docs/`, or a wrongly named folder under `features/`), **move or copy** them into `TARGET_DIR` using the **next free** variant names where collisions occur; prefer **move** when the source is clearly a duplicate staging area.

5. **Remove `.features/current` when safe** (after steps 1–4):
   - If **`current` is a symlink** (including one pointing at `TARGET_DIR`): **delete** the symlink only (e.g. `rm .features/current`). Do **not** delete contents inside `TARGET_DIR`.
   - If **`current` is a plain directory**: if **empty** after step 2’s moves, **`rmdir`**; if **not empty**, **report** what remains and **do not** delete the directory without user confirmation.
   - If **`current` does not exist**: nothing to remove.

After Stage 1, **`TARGET_DIR`** is the single canonical folder for this cycle’s artifacts and **`.features/current` should be absent** until a new cycle bootstraps it.

---

## Stage 2 — Collect inputs

1. Open **`TARGET_DIR`** (Stage 1 should have removed `.features/current`; do not rely on it).
2. Check which of these exist (including suffixed variants `*_a.md`, `*_b.md`, …):
   - `1_ProductRequirementsDocument*.md`
   - `2_Plan*.md`
   - `3_Implementation*.md`
   - `4_Review*.md`
3. Pull missing content from **conversation context** or legacy paths only when it was actually produced this cycle — **do not fabricate**.

Before proceeding, confirm:

> **Documenting:** [Feature name]  
> **Feature folder:** `.features/<DATE>_<SanitizedName>/`  
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

[What the review skill found; fixed, deferred, or accepted — or state that no review ran.]

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

Maintain **`.features/README.md`** at the workspace root (create if missing).

The index lists **completed** folders (`YYYY-MM-DD_Name`) and states that **there is no active cycle** until someone runs **ideate** (or another skill’s bootstrap), which recreates **`.features/current/`**.

**Structure:**

```markdown
# Feature cycles index

Active work in progress: **none** (after **document**, `.features/current` is removed; the next cycle recreates it via **ideate** or bootstrap)

---

## Archived cycles

### YYYY-MM-DD_FeatureSlug
**Folder:** `.features/YYYY-MM-DD_FeatureSlug/`  
**Summary:** [1–2 sentences]

---
```

**Append** or **update** an entry for this cycle’s **`TARGET_DIR`** when documentation is complete (summary from `0_Overview` or one line). **Append-only** for past entries — do not remove older rows unless correcting a clear mistake.

If **`.cursor/docs/README.md`** still exists from legacy docs, you may add a one-line pointer to `.features/README.md` rather than duplicating content.

---

## Stage 6 — Sign-off prompt

End with:

---

**Documentation complete.**

The following have been saved under **`.features/<DATE>_<SanitizedName>/`** (the **`.features/current`** symlink or folder was removed after consolidation):

- **`0_Overview*.md`** — plain English narrative (**this skill only**)
- **`1_ProductRequirementsDocument*.md`** — present or noted missing
- **`2_Plan*.md`** — present or noted missing
- **`3_Implementation*.md`** — present or noted missing
- **`4_Review*.md`** — present or noted missing

Index updated at **`.features/README.md`** (or noted if skipped).

- [ ] Overview accurately captures what was built and why  
- [ ] Known limitations and gotchas are correctly recorded  
- [ ] Review findings and resolutions are accurately summarized  

Reply **"approved"** to close out this cycle, or tell me what to adjust.

---

## Rules

- **`0_Overview`** is **only** produced by **document** (e.g. `/document` in Cursor).
- Never fabricate missing PRD/plan/implementation/review text — note gaps in the overview and sign-off.
- **Always** run Stage 0–1 so the cycle has a **dated project folder** and artifacts are **inside** it; **remove `.features/current`** when step 5 allows — do not leave an active `current` pointing at an archived cycle after this skill completes.
- Prefer **next-free** version suffixes `_a`, `_b`, `_c` **before** `.md`.
