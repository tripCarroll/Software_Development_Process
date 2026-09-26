# Software development process

Agentic skills for your software development projects.

## How to use this thing

These skills are designed to help you think intentionally about the software that you are building (several of these skills are pulled directly or heavily modified from [Matt Pocock's agentic skills](https://github.com/mattpocock/skills)).

## Skills: Product planning

`.docs/` is your product record, contains your ADRs, and is where your development cycles will be stored. An **Initiative** is the product; a **Project** is one area of it.

| Skill | When |
|-------|------|
| [`/repo-setup`](skills/repo-setup/SKILL.md) | The repo needs a `.docs` layout, or Linear should be pulled into it |
| [`/draft-initiative`](skills/draft-initiative/SKILL.md) | The product itself is being defined |
| [`/draft-project`](skills/draft-project/SKILL.md) | One area of the product is being defined |

## Skills: Dev cycle

A dev cycle is a single slice of work. Artifacts from a cycle will be saved until the cycle is closed out in the /devcycle_5_document skill. Once a cycle is closed out, it's documents, linked to it's parent project, and it's cycle folder is removed. 

| Skill | When |
|-------|------|
| [`/devcycle_0_context`](skills/devcycle_0_context/SKILL.md) | Mapping one area of the codebase |
| [`/devcycle_1_ideate`](skills/devcycle_1_ideate/SKILL.md) | A rough idea needs a PRD |
| [`/devcycle_2_plan`](skills/devcycle_2_plan/SKILL.md) | The work needs a phased implementation plan |
| [`/devcycle_3_implement`](skills/devcycle_3_implement/SKILL.md) | A plan is ready to execute |
| [`/devcycle_4_review`](skills/devcycle_4_review/SKILL.md) | An implementation needs a cold read, should be done in a fresh chat |
| [`/devcycle_5_document`](skills/devcycle_5_document/SKILL.md) | A finished cycle should become its summary and a changelog entry |

## Skills: Misc

Use these skills to learn, prove ideas, and get clarification.

| Skill | When |
|-------|------|
| [`/domain-modeling`](skills/domain-modeling/SKILL.md) | A domain term is unresolved, `CONTEXT.md` is being edited, or an ADR is being recorded |
| [`/grill-me`](skills/grill-me/SKILL.md) | A plan, decision, or idea needs a stress test |
| [`/grill-with-docs`](skills/grill-with-docs/SKILL.md) | That stress test should also write glossary terms and ADRs |
| [`/wayfinder`](skills/wayfinder/SKILL.md) | The work is bigger than one session, and the way to the destination is still open |
| [`/handoff`](skills/handoff/SKILL.md) | This conversation should be compacted for another agent |
| [`/teach`](skills/teach/SKILL.md) | The user wants to learn something. |
| [`/wait-what`](skills/wait-what/SKILL.md) | The last message did not land |
| [`/writing-for-agents`](skills/writing-for-agents/SKILL.md) | A skill, `AGENTS.md`, or `CLAUDE.md` is being written or edited |
