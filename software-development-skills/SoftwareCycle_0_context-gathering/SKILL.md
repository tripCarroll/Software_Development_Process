---
name: SoftwareCycle_0_context-gathering
description: Scans a focused codebase area and writes a structured context map to .docs/features/ so downstream ideation, planning, and implementation stay grounded. Use when the user runs /SoftwareCycle_0_context-gathering (Cursor), asks to map a path or domain, or wants context exploration before other pipeline skills (any host).
---

## Portable usage (Cursor & Claude)

Use in **Cursor** (`.cursor/skills/` or `~/.cursor/skills/`, optional slash commands) or **Claude** (project instructions, Claude Code `.claude/skills/<name>/SKILL.md`, or pasted workflow). **`[workspace-root]`** is the repository or project root for the active session.

**`/SoftwareCycle_0_context-gathering`** is Cursor shorthand; the same applies when the user asks to “gather context”, “map this area”, or “scan the codebase” before ideation or planning.

---

You are acting as a codebase analyst. Your job is to scan a focused area of the project, extract what matters, and produce a context map that gives any downstream skill a clear, accurate mental model of what exists — without overwhelming it with noise.

This is not a summary for the user to read. It is working memory for the agent. Write it accordingly: precise, structured, and scannable. Every statement should be something a downstream skill could act on.

**Numbered feature artifacts:** Do **not** save context maps as `0_Overview` or any `0_` file. **`0_Overview.md` is produced only by the SoftwareCycle_5_document skill.** Save context maps as `notes-[kebab-area-name].md` in the **active feature folder** (see Stage 0).

---

## Invocation

The user will invoke this skill with a focus area. Examples:

- `/SoftwareCycle_0_context-gathering src/components/button` (Cursor)
- `SoftwareCycle_0_context-gathering the token pipeline`
- `map the authentication flow`
- Whole project — see scope guidance below (e.g. focus `.`)

If no area is specified, ask before scanning: "What area should I focus on? A path, a feature domain, or a concept?"

---

## Stage 0 — Feature folder (resolve or create)

Resolve **`FEATURE_DIR`** before scanning. Context maps are saved **inside the dated feature folder** — never under **`.docs/features/current/`**.

**Save path:** `[workspace-root]/.docs/features/YYYY-MM-DD_SanitizedName/notes-[kebab-area-name].md`

1. Ensure **`.docs/features/`** exists (create if missing).
2. If the user or conversation names a folder under **`.docs/features/`** matching **`^\d{4}-\d{2}-\d{2}_.+`**, set **`FEATURE_DIR`** to it — **stop Stage 0** and continue to Stage 1.
3. Else, list subdirectories of **`.docs/features/`** matching **`^\d{4}-\d{2}-\d{2}_.+`**:
   - If **exactly one** exists, set **`FEATURE_DIR`** to it — **stop Stage 0**.
   - If **multiple** exist, prefer the one **most recently modified** that lacks **`0_Overview.md`** (cycle still in progress). If still ambiguous, **ask the user** which folder — do not guess.
4. If **no** matching folder exists: **ask the user:** *"What should we name this feature? (Used for the folder `YYYY-MM-DD_<name>`.)"* **Do not scan until the user replies.**
5. **Create** **`FEATURE_DIR`** from the user's answer (same convention as **SoftwareCycle_1_ideate**):
   - **`DATE`:** `YYYY-MM-DD` (prefer session **"today"**).
   - **`SanitizedName`:** from the feature name — allow letters, numbers, hyphen, underscore; spaces → `_`; strip or replace other characters; fallback `feature`.
   - If **`.docs/features/DATE_SanitizedName`** already exists and you need a new distinct folder, append `_2`, `_3`, … until unused.
   - **`mkdir -p FEATURE_DIR`**. Do **not** create **`.docs/features/current/`**.

**Legacy:** If **`.docs/features/current`** is a symlink to a dated folder, use that folder as **`FEATURE_DIR`** for this session only — do not create new `current` symlinks.

---

## Stage 1 — Scope assessment

Before scanning, determine the right scope:

- If a specific path is given, scan that directory and one level up for context
- If a feature domain or concept is given, identify the relevant files first by searching for likely entry points, then confirm your scan plan with a one-line note: "Scanning: `src/components/form`, `src/tokens/`, `src/utils/validation.ts`"
- If the whole project is given (`.`), scan the top-level structure only — do not recurse deeply. Surface the shape of the project, not every file.

---

## Stage 2 — Scan

Examine the following, scoped to the target area:

**Directory & file structure**
- What files and folders exist in scope
- Naming conventions in use
- Any files that look like entry points, exports, or index files

**README and docs**
- Any README, CHANGELOG, or docs files within or adjacent to scope
- Extract: purpose statements, usage examples, known limitations, version notes

**Existing component/module patterns**
- How are components or modules structured (file layout, naming, exports)
- What patterns are consistent across files (e.g. always a `.types.ts`, always a default export, always co-located tests)
- Any patterns that look inconsistent or like they're in transition

**Key config files**
- Scan for `package.json`, `tsconfig.json`, `.eslintrc`, `vite.config`, `rollup.config`, or equivalents if present at or near the scope root
- Extract: relevant dependencies, compiler options, aliases, build targets — only what's pertinent to the scoped area

---

## Stage 3 — Produce the context map

Write the context map in Markdown. Use the structure below exactly. Keep each section tight — this is a reference document, not a report. Omit sections that have nothing relevant to report rather than filling them with placeholder text.

---

```markdown
# Context map: [Area name]

**Scanned:** [Date and time]  
**Scope:** [The path(s) or domain you scanned]  
**Project:** [Project name from package.json or root folder name]  

---

## Project shape

[2–4 sentences on the overall project: what it is, what stack it uses, how it's structured at a high level. Only include if scanning the whole project or if the area's role within the larger project is non-obvious.]

---

## What exists in scope

[Structured file/folder summary. Use a short tree or bulleted list. Note the purpose of key files in a parenthetical. Example:]

- `src/components/button/`
  - `Button.ts` — main component definition
  - `Button.types.ts` — prop types and variant enums
  - `Button.stories.ts` — Storybook stories
  - `index.ts` — public export

---

## Patterns in use

[Bulleted list of conventions the downstream agent must follow to stay consistent. Be specific. Examples:]

- Components always export a default and a named type from `index.ts`
- Token references use CSS custom properties, never hardcoded hex values
- All async functions return a typed `Result<T, E>` rather than throwing

---

## Relevant config

[Only what's pertinent to the scoped area. Extract specific values, not just file names. Examples:]

- TypeScript: `strict: true`, path alias `@components` → `src/components`
- Build target: `es2020`, output as ESM only
- Peer deps: `lit@^3.0`, `@lit/reactive-element@^2.0`

---

## Docs & notes

[Anything from README, CHANGELOG, or inline docs worth surfacing. Keep it to bullet points. Examples:]

- README states that all new components must pass axe-core accessibility audit
- CHANGELOG notes that `v2.3` deprecated the `size` prop in favor of `scale`
- Known limitation: Shadow DOM encapsulation prevents external animation of internals

---

## Gaps & unknowns

[Things you couldn't determine from scanning — missing docs, ambiguous patterns, files that look stale or orphaned. A downstream skill should treat these as questions to resolve before acting.]

---

## Loaded for downstream use

The following context is now active for this session:
- Stack: [e.g. Lit + TypeScript, Web Components, Rollup]
- Patterns: [one-line summary of key conventions]
- Constraints: [one-line summary of hard limits]
- Scope boundary: [what's in scope vs. what's adjacent]
```

---

## Stage 4 — Save and confirm

Save the context map to **`FEATURE_DIR`** (Stage 0 must be complete):

```
.docs/features/YYYY-MM-DD_SanitizedName/notes-[kebab-area-name].md
```

**If the file already exists:** read it first, then **append** the new context map — do not overwrite prior content and do not create `_a`, `_b`, or other variant filenames. Separate scans with a horizontal rule (`---`), then the full Stage 3 map for the new scan (each scan gets its own `# Context map: [Area name]` heading and **Scanned** timestamp).

**If the file does not exist:** write the Stage 3 map as the full file contents.

**Do not wait for user approval** before saving. As soon as the map content is complete, write or append in the same turn. The user reviews and edits the markdown on disk if they want changes.

After saving, output a short confirmation in plain prose — not the full map again. Example:

> Context appended to `.docs/features/2026-08-20_button-refactor/notes-button.md`.  
> Active context: Lit + TypeScript Web Components project. Button component follows a 4-file pattern with strict TypeScript and CSS custom property tokens. Shadow DOM encapsulation is a known constraint. Ready for **SoftwareCycle_1_ideate** or **SoftwareCycle_2_plan**.

The full map is on disk. The confirmation is what loads into context — keep it dense and usable.

---

## Rules

- Never invent or infer things you didn't find. If something is unclear, put it in Gaps & unknowns.
- Do not summarize every file — surface what's meaningful for a developer about to add or change something.
- If a pattern is inconsistent, say so explicitly. Downstream skills need to know whether to follow the pattern or flag it.
- Ask the user only when required: **feature name** (Stage 0, if no active folder) or **scan scope** (Invocation, if no area given). Do not ask other questions during the scan unless scope is genuinely ambiguous.
- **Persist without approval:** write the context map file as soon as it is ready; do not ask the user to confirm the map before saving (feature naming in Stage 0 is separate).
- Always re-scan the codebase fresh — do not skip scanning because prior notes exist on disk; append the new scan to the existing **`notes-[kebab-area-name].md`** file when present.
