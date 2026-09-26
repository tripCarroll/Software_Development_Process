# `.docs` file formats

Bracketed text is guidance to replace with drafted content. Omit a section only where this file says it is optional; where a required section has no confirmed content, write a single `TODO:` line naming the missing information.

## `Initiative.md`

The product: what it is, why it exists, who it serves. In Linear this is an _Initiative_.

```markdown
# [Product title]

## Overview
[2–4 sentences: what this software is and what it does, for someone who has never seen it.]

## Problem
[The pain today, who feels it, and why it is worth solving now.]

## Goals
- [An outcome the product exists to achieve, tied to the Problem]

## Users
- **[User type]**: [who they are and what they need from the product]

## Features
- [[Project title]](projects/[projectTitle]/[projectTitle].md): [one line on what this area of the product does]
- **[Feature name]**: [one line on what it lets users do]

## Links
- **Linear initiative:** [[Initiative name]]([url])
- **Linear team:** [Team name] (`[TEAM_KEY]`)
- **Linear projects:**
  - [[Project title]]([url]) → [`projects/[projectTitle]/[projectTitle].md`](projects/[projectTitle]/[projectTitle].md)
```

**Features** holds two kinds of bullet. A project link covers one area of the product. A plain `**[Feature name]**` bullet is a feature no project has claimed yet; the project that claims it replaces the bullet with its own link.

`## Links` is optional: include it only when Linear is connected.

## `projects/[projectTitle]/[projectTitle].md`

One area of the product, usually a section or feature set. In Linear this is a _Project_.

`[projectTitle]` is the project title normalized: spaces become hyphens; keep only letters, numbers, hyphens, and underscores. The folder and the file share it.

```markdown
# [Project title]
[1–2 sentences: which part of the product this is.]

## Owners
- [Name] (lead)
- [Name]

## Goal
[What this area achieves for the users, tied to the problem they need solved.]

## Users
- **[User type from Initiative.md]**: [what this project gives them]

## Scope
**In scope**
- [What this project covers]

**Out of scope**
- [What it leaves to other projects or to later]

## Milestones
- **[Milestone name]**: [what is true once it is reached] _(target: [YYYY-MM-DD])_

## Features

### [Feature title]
[What the feature does today, in product terms. End with the main code path in backticks.]

## Planned features

### [Feature title]
[What the feature will let users do, and the milestone that delivers it.]

## Links
- **Linear project:** [[Project name]]([url])

## Changelog
```

**Features** lists only what the code shows exists. Work not yet built goes under **Planned features**; when a dev cycle ships one, **devcycle_5_document** moves it to Features.

Optional sections: `## Milestones` and `## Planned features` when empty, the milestone target date when none is set, and `## Links` unless Linear is connected. `## Changelog` starts empty; **devcycle_5_document** appends one entry per finished dev cycle.

## Root index `.docs/Initiative.md` (per-product layout only)

Describes the whole repository and points to each product's own `.docs`.

```markdown
# [Repository title]

## Overview
[2–4 sentences: what the repository holds and how its products relate.]

## Products
- [[Product title]]([package path]/.docs/Initiative.md): [one line on what the product is and who it serves]

## Shared packages
- `[package path]`: [what it provides, and which products use it]
```
