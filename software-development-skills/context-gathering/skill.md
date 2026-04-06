---
name: context-gathering
description: Scans a focused codebase area and writes a structured context map to .features/ so downstream ideation, planning, and implementation stay grounded. Use when the user asks to run /context-gathering, map a path or domain, or explore an area before other pipeline skills.
---

You are acting as a codebase analyst. Your job is to scan a focused area of the project, extract what matters, and produce a context map that gives any downstream skill a clear, accurate mental model of what exists — without overwhelming it with noise.

This is not a summary for the user to read. It is working memory for the agent. Write it accordingly: precise, structured, and scannable. Every statement should be something a downstream skill could act on.

**Numbered feature artifacts:** Do **not** save context maps into **`.features/current/`** as `0_Overview` or any `0_` file. **`0_Overview.md` is produced only by `/document`.** Context maps belong under **`.features/`** only (see Stage 4).

---

## Invocation

The user will invoke this skill with a focus area. Examples:

- `/context-gathering src/components/button`
- `/context-gathering the token pipeline`
- `/context-gathering authentication flow`
- `/context-gathering .` (whole project — see scope guidance below)

If no area is specified, ask before scanning: "What area should I focus on? A path, a feature domain, or a concept?"

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

Save the context map to:

```
.features/current/context-map-[kebab-area-name].md
```

Create the `.features/` directory if it doesn't exist.

After saving, output a short confirmation in plain prose — not the full map again. Example:

> Context map saved to `.features/current/context-map-button.md`.  
> Active context: Lit + TypeScript Web Components project. Button component follows a 4-file pattern with strict TypeScript and CSS custom property tokens. Shadow DOM encapsulation is a known constraint. Ready for `/ideate` or `/plan`.

The full map is on disk. The confirmation is what loads into context — keep it dense and usable.

---

## Rules

- Never invent or infer things you didn't find. If something is unclear, put it in Gaps & unknowns.
- Do not summarize every file — surface what's meaningful for a developer about to add or change something.
- If a pattern is inconsistent, say so explicitly. Downstream skills need to know whether to follow the pattern or flag it.
- Do not ask the user questions during the scan unless the scope is genuinely ambiguous before you start.
- Always re-scan fresh — do not rely on prior context map files from previous sessions, even if they exist on disk.