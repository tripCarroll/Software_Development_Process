---
name: handoff
description: Compact the current conversation into a handoff document for another agent to pick up.
argument-hint: "What will the next session be used for?"
disable-model-invocation: true
---

Write a handoff document summarising the current conversation so a fresh agent can continue the work. Save to the temporary directory of the user's OS - not the current workspace.

Include a "suggested skills" section in the document, naming the slash commands the user should run in the next session (e.g. `/devcycle_4_review`). Many skills are user-invoked, so the next agent cannot start them itself.

Do not duplicate content already captured in other artifacts (specs, plans, ADRs, issues, commits, diffs). Reference them by path or URL instead.

If a dev cycle is active, record its `DOCS_ROOT`, `CYCLE_DIR`, and `cycleName` so the next session can skip cycle-folder resolution, and list each cycle artifact by path. If the handoff falls mid-step, before that step's artifact is written, list which plan tasks are done and which remain.

Redact any sensitive information, such as API keys, passwords, or personally identifiable information.

If the user passed arguments, treat them as a description of what the next session will focus on and tailor the doc accordingly.
