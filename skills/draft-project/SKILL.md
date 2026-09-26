---
name: draft-project
description: Grill the user on one area of the product and draft it as .docs/projects/[projectTitle]/[projectTitle].md, linked from Initiative.md and optionally pushed to Linear as a Project.
argument-hint: "Optional: the project title, or the Initiative feature it starts from"
disable-model-invocation: true
---

You are shaping one **Project**: an area of the product, usually a section or feature set, with its own owners, goal, scope, and features. In Linear this is a _Project_ inside an _Initiative_. The result is `projects/[projectTitle]/[projectTitle].md` under the docs root, linked from `Initiative.md`, which the devcycle skills later append to as cycles ship.

The user owns every decision in the draft. Your job is to surface those decisions, press on vague answers, and write down only what the user confirmed.

Read [`../repo-setup/DOCS-FORMAT.md`](../repo-setup/DOCS-FORMAT.md) before drafting: it defines the project file format, how a title becomes `[projectTitle]`, and how `Initiative.md` links to projects.

## Stage 1 — Gather context

1. **Docs root.** Find every `.docs/` folder in the repo, skipping dependency and build directories. With one, that is the docs root. With several (a per-product monorepo), ask which product this project belongs to. With none, the docs root is `.docs/` at the repo root.
2. **Initiative.** Read `Initiative.md` in the docs root. Note its Problem, Goals, and Users, and every plain `**[Feature name]**` bullet under Features: these are the unclaimed features, the _seeds_ this project can start from. If the file is missing or holds only placeholder text, suggest `/draft-initiative` first, and continue without it only if the user asks.
3. **Sibling projects.** Read the Goal and Scope of every existing project file, so this project's boundaries can be drawn against theirs.
4. **Existing file.** If the argument or the user names a project whose file already exists, that file is the starting draft: Stage 2 grills its gaps and weak spots.
5. **Code.** If the repo holds code for this area, skim it. Each feature you find there is a _hypothesis_ for Features; everything else discussed goes to Planned features.

Stage 1 is complete when the docs root is settled and the seeds, sibling boundaries, and code hypotheses are noted.

## Stage 2 — Grill

Read [`../grill-me/SKILL.md`](../grill-me/SKILL.md) and follow it. The first round asks which seeds this project claims, unless the argument already names them. Seed the design tree with one branch per section of the project file:

- **Title and overview**: the project's name and which part of the product it is.
- **Owners**: who is accountable, and which one is the lead.
- **Goal**: what this area achieves for its users, traced to the Initiative's Problem and Goals.
- **Users**: which of the Initiative's user types this project serves. A user type the Initiative lacks is a change to `Initiative.md`; put it to the user.
- **Scope**: what is in and what is out. Every in-scope item belongs to this project alone; an overlap with a sibling project is an open branch.
- **Milestones**: the checkpoints on the way to the Goal, each with what is true once it is reached.
- **Features and Planned features**: what the code already does for this area, confirmed against the code, and what is still to build, each tied to a milestone.

Press on answers that stay abstract. "Better onboarding" is a hope; "a new bookkeeper imports their client list in one step" is a feature. A Goal no feature serves, or a feature outside Scope, is an open branch.

Stage 2 is complete when every section has a confirmed answer, the design tree's frontier is empty, and the user confirms you have reached a shared understanding.

## Stage 3 — Draft and confirm

Send one message containing:

1. The project file, drafted in the `DOCS-FORMAT.md` format, leaving out `## Links` (Stage 5 adds it) and with `## Changelog` empty. When replacing a file with real content, list each section you changed and how.
2. The change to `Initiative.md`: the claimed seed bullets replaced by one project link, in the `DOCS-FORMAT.md` format, plus any new user type from Stage 2.

Revise and re-send until the user approves both.

Stage 3 is complete when the user approves the project file and the `Initiative.md` change.

## Stage 4 — Write

In the docs root, write:

- `projects/[projectTitle]/[projectTitle].md`, the approved draft.
- `projects/[projectTitle]/cycles/.gitkeep`, if `cycles/` has no files.
- `Initiative.md`, with the approved change.

Stage 4 is complete when every file on disk matches the approved drafts.

## Stage 5 — Push to Linear (optional)

Ask whether to push the Project to Linear. On no, skip to Stage 6.

The Linear tools come from the Linear MCP server: the namespace whose tools include `save_project`. If it is missing or needs authentication, tell the user and skip to Stage 6.

1. **Team and Initiative.** Take the team and the Linear initiative from `## Links` in `Initiative.md`. Where either is missing, call `list_teams` or `list_initiatives` and ask, offering **None** for the initiative; suggest `/draft-initiative` to push the Initiative first.
2. **Existing project.** Call `list_projects` and look for one with the same name. If found, ask whether to update it or create a new one.
3. **Missing fields.** In one message, ask for what the file does not hold: **status** (default `Planned`), **start date** and **target date** (both optional).
4. **Project.** Call `save_project` with:
   - `name`: the title.
   - `summary`: the overview condensed to 255 characters or fewer.
   - `description`: the file's body below the title, without `## Changelog`.
   - `addTeams` and `leadTeam`: the team. `lead`: the Owners entry marked `(lead)`; the other owners stay in the file only, since a Linear project has one lead.
   - `addInitiatives`: the initiative, unless **None**.
   - the fields from step 3, plus `id` when updating.
5. **Milestones.** When updating, call `list_milestones` for the project first. For each entry under `## Milestones`, call `save_milestone` with `project`, `name`, `description`, and `targetDate`, passing `id` for a milestone that already exists by that name.
6. **Links.** Add `## Links` to the project file, just above `## Changelog`, with the project's URL. If `Initiative.md` has a `## Links` section, add the project under **Linear projects**, in the `DOCS-FORMAT.md` format.

Stage 5 is complete when the Linear project exists with every milestone, is attached to the initiative (unless **None**), and its URL is recorded in both files.

## Stage 6 — Report

In short prose, report the files written or changed, the Linear project URL if pushed, and every remaining `TODO:`.

If `Initiative.md` still has unclaimed feature bullets, suggest `/draft-project` for the next one. Otherwise suggest `/devcycle_0_context` to start the first dev cycle on this project.
