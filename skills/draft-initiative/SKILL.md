---
name: draft-initiative
description: Grill the user on the product they are conceptualizing and draft it as .docs/Initiative.md, optionally pushing it to Linear as an Initiative.
argument-hint: "Optional: the initiative name or a one-line pitch"
disable-model-invocation: true
---

You are turning a product idea into an **Initiative**: the document that says what the product is, what problem it solves, and who it serves. In Linear this is an _Initiative_. The result is `.docs/Initiative.md`, the file `repo-setup` and the devcycle skills read, written so it can be pushed to the user's project management tool as-is.

The user owns every decision in the draft. Your job is to surface those decisions, press on vague answers, and write down only what the user confirmed.

## Stage 1 — Scan the repo

If the repository holds code or docs, skim them so your questions start from what already exists: README, existing `.docs/`, package manifests, and top-level folders. Skip dependency and build directories. An empty or near-empty repo skips this stage.

Note what you find about the product's purpose, users, and features. Each note is a _hypothesis_ to put to the user in Stage 2.

If `.docs/Initiative.md` already exists:

- Placeholder text only (template guidance, no real product content): it will be replaced.
- Real content: treat it as the starting draft. Stage 2 grills the gaps and weak spots; Stage 3 shows what changes.
- A root index (it has `## Products`): ask which product this initiative covers and target that product's `.docs/Initiative.md` instead.

Stage 1 is complete when every hypothesis you will raise is noted and the target file path is settled.

## Stage 2 — Grill

Read [`../grill-me/SKILL.md`](../grill-me/SKILL.md) and follow it. Seed its design tree with one branch per section of the Initiative:

- **Title**: the product or initiative name.
- **Overview**: what the software is and does, for someone who has never seen it.
- **Problem**: the pain that exists today, who feels it, and why it is worth solving now.
- **Goals**: the outcomes the product exists to achieve, each tied to the Problem.
- **Users**: each distinct user type and what they need from the product.
- **Features**: the capabilities the product offers those users to reach the Goals.

Press on answers that stay abstract. "Small businesses" is a hypothesis; "solo bookkeepers juggling ten or more clients" is a user. A Goal no Feature serves, or a Feature no Goal needs, is an open branch.

Stage 2 is complete when every section has a confirmed answer, the design tree's frontier is empty, and the user confirms you have reached a shared understanding.

## Stage 3 — Draft and confirm

Draft the Initiative in the `Initiative.md` format from [`../repo-setup/DOCS-FORMAT.md`](../repo-setup/DOCS-FORMAT.md), leaving out `## Links` (Stage 5 adds it). Features are plain `**[Feature name]**` bullets, since no project has claimed them yet; keep any project links an existing file already has.

Send the draft in one message. When replacing a file with real content, list each section you changed and how. Revise and re-send until the user approves.

Stage 3 is complete when the user approves the draft.

## Stage 4 — Write

Write the approved draft to the target file from Stage 1, creating `.docs/` if needed.

Stage 4 is complete when the file on disk matches the approved draft.

## Stage 5 — Push to Linear (optional)

Ask whether to push the Initiative to Linear. On no, skip to Stage 6.

The Linear tools come from the Linear MCP server: the namespace whose tools include `save_initiative`. If it is missing or needs authentication, tell the user and skip to Stage 6.

1. Call `list_initiatives` and look for one with the same name. If found, ask whether to update it or create a new one.
2. In one message, ask for the fields the file does not hold: **owner** (default `me`), **target date** (optional), **lead team** (call `list_teams` to offer the choices), and **status** (default `Proposed`).
3. Call `save_initiative` with:
   - `name`: the title.
   - `summary`: the Overview condensed to 255 characters or fewer.
   - `description`: the file's body below the title.
   - the fields from step 2, plus `id` when updating.
4. Append a `## Links` section to the file, in the `DOCS-FORMAT.md` format, with the initiative's URL and lead team.

Stage 5 is complete when the initiative exists in Linear and its URL is recorded in the file.

## Stage 6 — Report

In short prose, report the file path written, the Linear initiative URL if pushed, and every remaining `TODO:`.

Suggest `/draft-project` as the next step, to shape the Features into Projects one at a time, or `/repo-setup` to draft every project at once.
