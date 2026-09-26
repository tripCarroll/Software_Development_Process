---
name: repo-setup
description: Set up a repository's .docs — choose a single root .docs or one per product in a monorepo, pull the Initiative and Projects from Linear, and draft Initiative.md and the project files.
argument-hint: "Optional: the Linear initiative name or URL"
disable-model-invocation: true
---

You are setting up the repository's `.docs`: the product record that the devcycle skills read and write. Draft real content from the user, Linear, existing docs, and the code, so the result reads as documentation of this product rather than a template.

Read [`DOCS-FORMAT.md`](DOCS-FORMAT.md) before writing any file.

## The `.docs` layout

```
.docs/
├── Initiative.md          # the product: overview, problem, goals, users, features, links
├── adr/                   # architecture decision records, created empty
│   └── .gitkeep
└── projects/
    └── [projectTitle]/
        ├── [projectTitle].md  # one area of the product: owners, goal, scope, features, changelog
        └── cycles/            # dev cycles, written by the devcycle skills
            └── .gitkeep
```

`DOCS-FORMAT.md` defines how a project title becomes `[projectTitle]`.

---

## Stage 1 — Assess the repo

Scan the repository, skipping dependency and build directories:

- **Existing docs**: README, CHANGELOG, `docs/`, wikis, and any `.docs/` anywhere in the tree. Read them as drafting input and leave them in place.
- **Workspace signals**: `workspaces` in `package.json`, `pnpm-workspace.yaml`, `lerna.json`, `nx.json`, `turbo.json`, `rush.json`, `[workspace]` in `Cargo.toml`, `go.work`, or top-level `apps/`, `packages/`, `services/` folders whose children carry their own manifests.
- **Package classification**: sort every workspace package into _product_ or _internal_. A **product** has its own users and goal, and ships on its own: a deployable app, a service, a library published for outside consumers. An **internal** package (shared UI, config, utilities, tooling) exists to serve other packages. Packages that serve the same users toward the same goal, like a web app and its API, together form one product.

Choose the layout:

- **Single**: no workspace, or a monorepo with one product. One `.docs/` at the repo root; internal packages are covered by its projects.
- **Per-product**: a monorepo with two or more products. Each product gets a full `.docs/` at its package root. A product spanning several packages puts its `.docs/` at their nearest common folder; when that folder is the repo root, ask the user where it goes. The repo root gets an index-only `.docs/Initiative.md` linking to each product and listing the internal packages.

Where a `.docs/` already exists at a chosen location, this run fills gaps: it creates missing files, and changes existing ones only as approved in Stage 3.

Stage 1 is complete when every workspace package is classified and every `.docs` location is chosen.

## Stage 2 — Pull from Linear

If the user passed an initiative or has already said whether the team uses Linear, use that. Otherwise ask whether this repo is tracked in Linear.

- **Yes**: follow **Pulling for `.docs`** in [`LINEAR.md`](LINEAR.md).
- **No**: skip this stage; projects come from the codebase in Stage 3.

Stage 2 is complete when each `.docs` location is matched to one Linear initiative or marked as having none, and the projects and milestones of every matched initiative have been read.

## Stage 3 — Draft and confirm

For each `.docs` location, draft:

- **Projects**: the Linear projects when Linear is connected. Otherwise, the product areas a product manager would name (a route group, a domain module, a service), found in the code. When an existing `Initiative.md` lists Features as plain `**[Feature name]**` bullets (written by `draft-initiative`), group those features into projects too; each feature lands in exactly one project.
- **Initiative.md**: Overview, Problem, Goals, Users, Features. Features become project links per `DOCS-FORMAT.md`. Keep an existing Problem section as written unless the user changes it.
- **[projectTitle].md** per project, in the `DOCS-FORMAT.md` format. Features describe what the product does today; confirm each against the code. A plain feature bullet or Linear milestone the code doesn't show yet goes under Planned features.

When sources disagree, trust them in this order: the user, then Linear, then existing docs, then the code. Mark every draft statement you inferred from code alone.

Send one message containing:

1. The layout decision with its evidence: the workspace signals found, each package's classification, and every `.docs` path.
2. For each `.docs`: the initiative title and the project list, each project tagged with its source (Linear, code, or draft features).
3. The Problem, Goals, and Users drafts, with direct questions for what no source answered. The code rarely says who the users are or what problem they have, so expect to ask.
4. Any existing file you propose to change, and the change.

Wait for the reply. Revise and re-confirm until the user approves.

## Stage 4 — Write

For each `.docs` location, write every file in the layout above, following `DOCS-FORMAT.md`: `Initiative.md`, `adr/.gitkeep`, and for each project `[projectTitle].md` and `cycles/.gitkeep`. In the per-product layout, also write the root index `.docs/Initiative.md`.

Stage 4 is complete when every file listed in the approved Stage 3 plan exists on disk.

## Stage 5 — Report

In short prose, report:

- each `.docs` path and the files written under it
- the Linear initiative and projects recorded in Links, if any
- every remaining `TODO:`, grouped by file

Suggest `/devcycle_0_context` as the next step.
