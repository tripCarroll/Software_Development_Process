# Linear

The Linear tools come from the Linear MCP server: the namespace whose tools include `list_initiatives`. If the server is missing or needs authentication, tell the user and offer to continue without Linear.

Linear _cycles_ are team sprints and are separate from dev cycles; leave them out.

This file has two parts: **Pulling for `.docs`** is repo-setup's Stage 2; **Wayfinding operations** is how the wayfinder skill uses Linear as its tracker.

## Pulling for `.docs`

| Linear | `.docs` |
|--------|---------|
| Initiative | `Initiative.md` |
| Project | `projects/[projectTitle]/[projectTitle].md` |
| Project lead | The `(lead)` entry under that file's `## Owners` |
| Project milestone | An entry under that file's `## Milestones` |

1. **Team.** Call `list_teams`. With several teams, ask which one owns this repo.
2. **Initiative.** Call `list_initiatives`. Match one initiative to each `.docs` location by the argument the user passed or by name. Ask the user to choose wherever the match is ambiguous, offering the candidates and **None**. Batch these choices with the team question into one message.
3. **Initiative content.** Call `get_initiative` for each match. Its description and content seed the Overview, Problem, Goals, and Users drafts.
4. **Projects.** Call `list_projects` with `initiative` set and `includeMilestones: true`, requesting the `name`, `summary`, `description`, `url`, `status`, `lead`, and `milestones` fields. Page through with `cursor` until every project is read. Each project that is not canceled becomes a project folder; completed projects stay, since they describe shipped product.
5. **Milestones.** Each project's milestones become its `## Milestones` entries, with target dates. A milestone's deliverables that the code already shows go under Features; the rest go under Planned features.
6. **Links.** Keep the initiative URL, team name and key, and every project URL for the `## Links` section of `Initiative.md`.

The pull is complete when every matched initiative's projects have been read and every URL needed for Links is in hand.

## Wayfinding operations

The team is the **Linear team** in the `## Links` section of `.docs/Initiative.md`. Every issue below is created in that team, and in the Linear project the effort belongs to (ask when the destination doesn't make it obvious). Refer to an issue by its title with its identifier (e.g. `ENG-42`) inside the link.

| Wayfinder | Linear |
|-----------|--------|
| Map | Issue labelled `wayfinder:map`; the map body is its description |
| Ticket | Sub-issue of the map, labelled `wayfinder:<type>` |
| Blocking | Native `blocks` / `blockedBy` relations |
| Claim | Assignee set to `"me"` |
| Resolution | Comment on the ticket, then state `completed` |
| Ruled out of scope | State `canceled` |
| Asset | Link attachment on the ticket |

**Labels.** Before the first map in a team, call `list_issue_labels` with `team` set and create any missing `wayfinder:map`, `wayfinder:research`, `wayfinder:prototype`, `wayfinder:grilling`, and `wayfinder:task` with `save_issue_label`.

**Create the map.** `save_issue` with `team`, `project`, `title`, `description` (the map body), and `labels: ["wayfinder:map"]`.

**Create a ticket.** `save_issue` with `team`, `project`, `title`, `description` (the `## Question` body), `parentId` set to the map, and `labels: ["wayfinder:<type>"]`.

**Wire blocking** (second pass, once every ticket has an identifier). `save_issue` with the ticket's `id` and `blockedBy` listing its blockers.

**Claim.** `save_issue` with the ticket's `id` and `assignee: "me"`, before any other work on it.

**Find the frontier.**
1. `list_issues` with `parentId` set to the map, `assignee: null`, `orderBy: "createdAt"`, and `fields: ["id", "title", "url", "statusType", "labels"]`. Keep issues whose `statusType` is neither `completed` nor `canceled`.
2. For each, `get_issue` with `includeRelations: true`. It is on the frontier when every `blockedBy` issue is `completed` or `canceled`.

The frontier's first issue, in creation order, is the default next ticket.

**Record a resolution.**
1. `save_comment` with `issueId` set to the ticket and the answer as `body`.
2. `save_issue` with the ticket's `id` and `state: "completed"`.
3. Update the map with `save_issue` using `patch`: an `insert_before` anchored on `## Not yet specified` adds the new Decisions-so-far line; a `replace` clears a graduated patch of fog.

**Rule a ticket out of scope.** `save_issue` with the ticket's `id` and `state: "canceled"`, then patch the map's **Out of scope** section.

**Attach an asset.** `save_issue` with the ticket's `id` and `links: [{ url, title }]`.
