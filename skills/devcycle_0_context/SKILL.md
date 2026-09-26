---
name: devcycle_0_context
description: Dev cycle step 0 — scan a codebase area and write a codebase map to the cycle folder.
disable-model-invocation: true
---

You are acting as a codebase analyst. Your job is to scan a focused area of the project, extract what matters, and produce a codebase map that gives any downstream skill a clear, accurate mental model of what exists — without overwhelming it with noise.

This is both a summary for the user to read to understand more about the repository as well as working memory for the agent. Write it accordingly: precise, structured, and scannable. Every statement should be something a downstream skill could act on.

---

## Invocation

The user will invoke this skill with a focus area. Examples:

- `/devcycle_0_context the token pipeline`
- `map the authentication flow`
- Whole project — see scope guidance below (e.g. focus `.`)

Before scanning you need a focus area plus the project and cycle name from Stage 0. Ask for everything missing in a single message. For a missing focus area: "What area should I focus on? A path, a feature domain, or a concept?"

---

## Stage 0 — Cycle folder

Read [`../devcycle_shared/CYCLE.md`](../devcycle_shared/CYCLE.md) and resolve **`CYCLE_DIR`** before scanning. It also defines artifact names and versioning.

---

## Stage 1 — Scope assessment

Before scanning, determine the right scope:

- If a specific path is given, scan that directory and one level up for context
- If a feature domain or concept is given, identify the relevant files first by searching for likely entry points, then state your scan plan in one line and continue without waiting: "Scanning: `src/components/form`, `src/tokens/`, `src/utils/validation.ts`"
- If the whole project is given (`.`), scan the top-level structure only — do not recurse deeply. Surface the shape of the project, not every file.

---

## Stage 2 — Scan

Examine the following, scoped to the target area:

**Directory & file structure**
- What files and folders exist in scope
- Naming conventions in use
- Any files that look like entry points, exports, or index files

**README and docs**
- Any README, CHANGELOG, or docs files within or adjacent to scope, plus the domain glossary (`CONTEXT.md` / `CONTEXT-MAP.md`) and ADRs in `.docs/adr/`
- Extract: purpose statements, usage examples, known limitations, version notes, domain terms, and standing decisions that bear on the scope

**Existing component/module patterns**
- How are components or modules structured (file layout, naming, exports)
- What patterns are consistent across files (e.g. always a `.types.ts`, always a default export, always co-located tests)
- Any patterns that look inconsistent or like they're in transition

**Key config files**
- Scan for `package.json`, `tsconfig.json`, `.eslintrc`, `vite.config`, `rollup.config`, or equivalents if present at or near the scope root
- Extract: relevant dependencies, compiler options, aliases, build targets — only what's pertinent to the scoped area

---

## Stage 3 — Produce the codebase map

Write the codebase map in Markdown. Use the structure below exactly. Keep each section tight — this is a reference document, not a report. Omit sections that have nothing relevant to report rather than filling them with placeholder text.

---

```markdown
# Codebase map: [Area name]

**Scanned:** [Date and time]  
**Scope:** [The path(s) or domain you scanned]  
**Repository:** [Name from package.json or root folder name]  

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

## Summary

- Stack: [e.g. Lit + TypeScript, Web Components, Rollup]
- Patterns: [one-line summary of key conventions]
- Constraints: [one-line summary of hard limits]
- Scope boundary: [what's in scope vs. what's adjacent]
```

---

## Stage 4 — Save and confirm

Save the codebase map to **`CYCLE_DIR/_notes-[cycleName].md`**. If the file already exists, keep its contents and append the new map after a horizontal rule (`---`); each scan keeps its own `# Codebase map: [Area name]` heading and **Scanned** timestamp.

After saving, output a short confirmation in plain prose — not the full map again. Example:

> Context appended to `.docs/projects/design-system/cycles/2026-08-20-button-refactor/_notes-button-refactor.md`.  
> Active context: Lit + TypeScript Web Components project. Button component follows a 4-file pattern with strict TypeScript and CSS custom property tokens. Shadow DOM encapsulation is a known constraint. Ready for **devcycle_1_ideate** or **devcycle_2_plan**.

The full map is on disk. The confirmation is what loads into context — keep it dense and usable.

---

## Rules

- Report only what you found. Mark anything inferred across files (patterns especially) as inferred; put unresolved questions in Gaps & unknowns.
- Surface what's meaningful for a developer about to add or change something, not a summary of every file.
- If a pattern is inconsistent, say so explicitly. Downstream skills need to know whether to follow the pattern or flag it.
- Ask the user only for the inputs listed under Invocation. During the scan, ask only if scope is genuinely ambiguous.
- Always re-scan the codebase fresh, even when notes for this cycle already exist on disk.