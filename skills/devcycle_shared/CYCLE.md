# Dev cycle conventions

Shared by every devcycle skill. Read this before reading or writing any cycle artifact.

## Cycle folder

Resolve **`CYCLE_DIR`**, the folder holding every artifact of one in-progress dev cycle. If the conversation already established it, use it. Otherwise:

0. **Docs root.** Find every `.docs/` folder in the repo, skipping dependency and build directories. A monorepo set up by **repo-setup** has one per product, plus an index-only `.docs/` at the repo root for repo-wide work.
   - One: that folder is **`DOCS_ROOT`**.
   - None: `DOCS_ROOT` is `.docs/` at the repo root, created in step 4.
   - Several: use the one the user or conversation names, or the one whose package contains the focus area. Otherwise ask which product this cycle is for, offering each `.docs/` by its package path.
1. **Project.** If the user or conversation names the project, use it. Otherwise, if `DOCS_ROOT/projects/` contains project folders, ask which project this cycle belongs to, offering:
   - each existing project folder
   - **New project**: ask for its name; the folder `DOCS_ROOT/projects/[projectTitle]/` is created in step 4
   - **No project**

   With no project folders, the cycle has no project; skip the question.
2. **Cycles directory.**
   - With a project: `DOCS_ROOT/projects/[projectTitle]/cycles/`
   - Without: `DOCS_ROOT/cycles/`
3. **Cycle name.** If the user or conversation names the cycle, use it. Otherwise ask *"What should we call this dev cycle?"*, offering the three newest folders in the cycles directory (by date prefix) to continue, alongside a new name. Folders are in-progress cycles; a finished cycle is a single `[date]-[cycleName].md` file. Ask this in the same message as any other missing input, and wait for the answer before continuing.
4. **Folder.** Normalize `cycleName`: spaces become hyphens; keep only letters, numbers, hyphens, and underscores.
   - If a folder in the cycles directory ends in `-[cycleName]` (case-insensitive), it is `CYCLE_DIR` and keeps its original date. With several matches, take the newest.
   - Otherwise `CYCLE_DIR` is `[date]-[cycleName]/`, with `[date]` as today in `YYYY-MM-DD`. Create it along with any missing parents.

Resolution is complete when `CYCLE_DIR` exists on disk and `DOCS_ROOT` and `cycleName` are set.

## Artifacts

| File | Written by | When the file already exists |
|------|------------|------------------------------|
| `_notes-[cycleName].md` | devcycle_0_context | Append the new scan |
| `1_ProductRequirementsDocument.md` | devcycle_1_ideate | Next free suffix |
| `2_Plan.md` | devcycle_2_plan | Next free suffix |
| `3_Implementation.md` | devcycle_3_implement | Next free suffix |
| `4_Review.md` | devcycle_4_review | Next free suffix |
| `[date]-[cycleName].md`, beside `CYCLE_DIR` in the cycles directory | devcycle_5_document | Ask: overwrite, or next free suffix |

**Next free suffix:** write `_a` before `.md` (`2_Plan_a.md`), then `_b`, `_c`, … When reading, the highest suffix is the current version.

Within `CYCLE_DIR`, each skill writes only its own artifact, as soon as it is ready and in the same turn. The user reviews and edits the markdown on disk, so writing never waits on approval.

## Domain language and decisions

The domain glossary (`CONTEXT.md` at the repo root, or the files a `CONTEXT-MAP.md` points to) and the ADRs in `.docs/adr/` live outside the cycle and outlast it; the **domain-modeling** skill owns both. When they exist, use the glossary's terms in every artifact and treat ADRs as standing decisions.

Artifacts from before this layout may live in `.docs/features/YYYY-MM-DD_Name/`; read them there when the user points to them.
