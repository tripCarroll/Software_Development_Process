![Process Breakdown](imgs/software_build_phases.svg)

# Software development process skills

This repository holds **Cursor agent skills** for a full build cycle: grounding work in the codebase, shaping requirements, planning, implementing, reviewing, archiving, and improving the process itself. The goal is more reliable, consistent, and higher-quality output by giving the agent explicit stages, artifacts, and handoffs.

## Using these skills in Cursor

Skills are folders that contain a main instruction file. Cursor expects:

- **Personal:** `~/.cursor/skills/<skill-name>/SKILL.md`
- **Project:** `.cursor/skills/<skill-name>/SKILL.md`

In this repo the files are named `skill.md`. For Cursor, rename each to **`SKILL.md`** (or symlink/copy the directories into one of the paths above). A **symbolic link** to this repo’s `software-development-skills/<name>/` folder is more reliable than a macOS Finder alias.

Invoke skills by asking the agent in natural language (e.g. “run `/plan` for this PRD”) or whatever slash-command style you use in your setup. Each skill’s frontmatter includes a `command:` hint (e.g. `/ideate`).

---

## Recommended cycle

Typical order:

1. **`/context-gathering`** (optional but useful) — Map the relevant area of the codebase.
2. **`/ideate`** — Interview, then produce a signed-off **PRD**.
3. **`/plan`** — Turn the PRD into a phased **implementation plan**; sign-off before build.
4. **`/implement`** — Execute the plan task-by-task, tests alongside work; **implementation summary** (`IMPL-…md`).
5. **`/review`** — Best in a **fresh chat**: cold read of code against PRD, plan, and summary; structured findings.
6. **`/document`** — Collate artifacts into **`.cursor/docs/[feature-name]/`** and a readable **overview** plus index.
7. **`/retro`** (after a full cycle) — Meta review: how well the *skills* worked; proposed edits to skill files (you apply them).

---

## Skills in this repo

| Command | Skill | Role |
|--------|--------|------|
| `/context-gathering [area]` | **context-gathering** | Scans a path or domain and writes a structured **context map** to **`.cursor/context/context-map-….md`**, plus a short session summary so later steps match real patterns and config. |
| `/ideate` | **ideate** | Structured **interview** (small batches of questions), then a full **PRD** and refinement pass; explicit **sign-off** before planning. Output convention: `PRD-[feature].md` in `.cursor/docs/`. |
| `/plan` | **plan** | Reads the PRD (and context you’ve loaded); produces an **adaptive** phased plan (no boilerplate sections); refinement pass; **sign-off** before `/implement`. Output: `PLAN-[feature].md`. |
| `/implement` | **implement** | Requires a signed-off plan; executes **phase → task** order; **tests with each task**; stops on real ambiguity; writes **`IMPL-[feature].md`**. No scope improvisation or drive-by refactors. |
| `/review` | **review** | **Second-agent, cold** review: PRD/plan/summary + code; checklist (alignment, correctness, patterns, edge cases, tests, security, performance, a11y, etc.); severity-ranked issues; sign-off before archive. |
| `/document` | **document** | **Archivist**: gathers PRD, plan, impl summary, review; builds **`.cursor/docs/[feature]/`** with a standalone **overview** and maintains a **README index** of features. |
| `/retro` | **retro** | **Process audit** (not product quality): which skills felt wrong, evidence from artifacts + your experience; per-skill assessment; **plain-English change list** — does **not** edit skill files without your approval. |

---

## Where artifacts land

| Location | Contents |
|----------|----------|
| **`.cursor/context/`** | Context maps from `/context-gathering`. |
| **`.cursor/docs/`** | PRD, plan, and implementation summary files (`PRD-…`, `PLAN-…`, `IMPL-…`), and per-feature folders created by `/document`. |

Exact filenames are defined inside each skill; keep naming consistent so `/plan`, `/implement`, `/document`, and `/retro` can find evidence of the cycle.

---

## Status

These skills are written as a **coherent prototype pipeline**. `/retro` is explicitly there to tighten them based on real use. Adjust `SKILL.md` files as you learn what works for your team and stack.
