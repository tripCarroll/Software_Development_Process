---
name: initialize
description: Start or rotate the active feature cycle under .features. Asks for a feature name, creates a dated folder YYYY-MM-DD_Name for artifacts, links .features/current to it, and archives any previous cycle.
command: /initialize
---

You are setting up the **canonical workspace for one feature cycle**. Every other skill in the chain (`/ideate`, `/plan`, `/implement`, `/review`, `/document`) resolves artifact paths relative to **`.features/current/`** at the **repository (workspace) root**.

**`current` may be a symlink** to a dated folder (e.g. `2026-03-27_MyFeature`). Tools and skills should follow it; artifact paths stay **`.features/current/`** for compatibility.

---

## Feature artifact root

**Active path (resolve via symlink):** `[workspace-root]/.features/current/`

**Physical folder (this cycle):** `[workspace-root]/.features/YYYY-MM-DD_SanitizedFeatureName/`

All numbered artifacts for the **in-progress** cycle live under `current/` (same as the dated folder when the symlink is in place):

| # | Filename | Produced by |
|---|----------|-------------|
| (none) | — | `/context-gathering` saves to `.features/` only — not part of the numbered set |
| 0 | `0_Overview.md` | **`/document` only** |
| 1 | `1_ProductRequirementsDocument.md` | `/ideate` |
| 2 | `2_Plan.md` | `/plan` |
| 3 | `3_Implementation.md` (+ `_a`, `_b` …) | `/implement` |
| 4 | `4_Review.md` (+ `_a`, `_b` …) | `/review` |

**Versioning:** If the base file already exists (e.g. `3_Implementation.md`), write the next as `3_Implementation_a.md`, then `3_Implementation_b.md`, `3_Implementation_c.md`, etc. (suffix **before** `.md`).

---

## When to invoke

- **Start of a new feature cycle** — before or right after `/context-gathering`, so `/ideate` has a place to save the PRD.
- **Between cycles** — when finishing one feature and starting another: retire `current`, then create a new dated folder and relink.

---

## Naming and sanitization

- Let **`DATE`** be today’s date as **`YYYY-MM-DD`** (use the user’s “today” from context when available).
- **`SanitizedName`:** from the user’s feature name — allow letters, numbers, hyphen, underscore; replace spaces with `_`; strip or replace other characters; collapse repeats; **avoid empty string** (fallback `feature`).
- **Folder basename:** `DATE_SanitizedName` (e.g. `2026-03-27_OAuthRefresh`).

If `.features/DATE_SanitizedName` already exists, append a disambiguator: `_2`, `_3`, … until unused.

---

## Stage 0 — Ask for the new feature name

1. **Always ask first** (unless the user already gave an explicit name in the same message — then use that):  
   *What should we name this feature cycle?* (short label is fine; it becomes part of the folder name after the date.)
2. Compute **`DATE`**, sanitize to **`SanitizedName`**, resolve final **`TARGET_DIR`** = `.features/DATE_SanitizedName` (with disambiguation if needed).

Do **not** create the new folder until any previous `current` is handled (Stage 1).

---

## Stage 1 — Retire previous `current` (if it exists)

1. Resolve **workspace root** (the Cursor project root / git root).
2. Ensure `.features/` exists (create if missing).
3. If **`.features/current`** does **not** exist, skip to Stage 2.

4. If **`current` is a symbolic link** to a directory under `.features/`:
   - **Remove only the symlink** `current` (do **not** delete the target directory). The previous cycle remains archived as **`DATE_PreviousName/`** already.
   - Skip any extra rename prompts.

5. If **`current` is a plain directory** (legacy layout):
   - Ask the user for a **short name** for the work that was in `current` (e.g. `ArchivePythonRename`, `OAuthFix`) if they have not already provided one usable for archiving.
   - Sanitize that name the same way as for new cycles.
   - **Rename** (move) the directory:
     - **From:** `.features/current`
     - **To:** `.features/DATE_ArchiveSanitizedName`  
     Use the same **`DATE`** as today, or if that collides, use the disambiguator rule on the archive name side (`_2`, …).
   - Preserve the entire tree (including any `1_`…`4_` files already there).

---

## Stage 2 — Create dated folder and link `current`

1. Create **`TARGET_DIR`** as a directory (empty to start).
2. Add **`TARGET_DIR/README.md`** with:
   - Line 1: **Feature name** (as the user gave it, human-readable).
   - Line 2: **Started:** `DATE`.
   - Line 3: Note that **`/initialize`** retires `current` and starts a new dated folder; numbered artifacts `1_`–`4_` and (on `/document`) `0_Overview.md` go in this folder via **`.features/current/`**.
3. From **`.features/`**, set **`current`** to point at the new folder:
   - **Preferred:** `current` → symbolic link to **`TARGET_DIR`** using a **relative** target (e.g. `ln -sfn 2026-03-27_MyFeature current` from inside `.features`).
   - If symlinks are not available or fail, **fallback:** keep a real directory named `current` and state clearly in chat that the dated-folder layout could not be applied; copy the README content into `current/README.md` (degraded mode).

---

## Stage 3 — Confirm

Output a short confirmation:

> Feature workspace ready.  
> **Artifacts directory:** **`.features/<DATE>_<Name>/`**  
> **Active path (for skills):** **`.features/current/`** → same folder  
> Next: `/ideate`, `/plan`, etc. will save numbered artifacts there.

If a legacy folder was renamed in Stage 1, add one line:

> Previous cycle saved as **`.features/<DATE>_<ArchiveName>/`**

---

## Rules

- Paths are always relative to the **workspace root**, not the user home directory.
- Never write numbered artifacts (`0_`–`4_`) outside the active cycle directory (reachable as **`current/`**) during the cycle (except `/document` behavior described in `/document` skill).
- The name **`current`** is the stable entry point; the **dated folder** is the durable, browse-friendly archive name for that cycle’s artifacts.
