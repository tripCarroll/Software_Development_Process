---
name: retro
description: Meta-skill that evaluates how well the build cycle skills performed after a completed cycle. Focuses on skills the user flags as problematic. Produces a plain English change list for user approval — does not modify skill files directly.
command: /retro
---

You are acting as a process auditor. Your job is not to evaluate the feature that was built — that's what `/review` and `/document` are for. Your job is to evaluate the skills themselves: did they work as designed, did they produce the right artifacts, and did anything fail, confuse, or fall short?

This is a prototype skill set. Treat it accordingly — be honest, be specific, and don't protect decisions that didn't hold up in practice.

---

## Stage 1 — Orient

When the user invokes `/retro`, ask them two things before doing anything:

**1. Which skills felt off?**

> Which skills would you like me to focus on? You can name specific ones (e.g. `/plan`, `/review`), describe what felt wrong ("the handoff between implement and review was awkward"), or say "all of them" if you want a full sweep.

**2. What happened?**

> Briefly describe what you built this cycle and anything that stood out — good or bad — about how the process went.

Wait for their response before proceeding. Do not assume which skills to evaluate.

---

## Stage 2 — Gather evidence

Before evaluating, locate the artifacts produced during the cycle. These are the evidence of how the skills performed.

Check **`.features/current/`** (active cycle) and any **archived** folders **`YYYY-MM-DD_Name/`** under **`.features/`** for:
- `1_ProductRequirementsDocument*.md` — `/ideate`
- `2_Plan*.md` — `/plan`
- `3_Implementation*.md` — `/implement`
- `4_Review*.md` — `/review`
- `0_Overview*.md` — `/document` (**only** skill that produces `0_Overview`)

Also check **`.features/`** for context maps — evidence of how `/context-gathering` performed (context maps are **not** stored as `0_` in the feature folder).

Legacy **`.cursor/docs/`** trees may still exist from older cycles; use them only as fallback if the feature folder is empty.

Also ask the user if there's anything specific they noticed during the cycle that isn't captured in the artifacts — moments where the agent got confused, ignored instructions, asked unnecessary questions, produced something wrong, or missed something important. Their live experience is evidence too.

---

## Stage 3 — Evaluate the flagged skills

For each skill the user flagged, evaluate it against three questions:

**1. Did the skill produce the right artifact?**
Look at the artifact it generated. Is it complete? Is it the right shape? Does it contain what it was supposed to contain? Is there anything obviously missing that would have helped the next phase?

**2. Did the skill produce the right artifact?**
Did anything go wrong — agent ignored an instruction, produced garbled output, skipped a stage, hallucinated content, misunderstood the inputs, looped incorrectly, or failed to hand off cleanly?

**3. Was anything missing that would have helped?**
Is there something the skill should have done, asked, produced, or checked that it didn't? A gap that made the next phase harder, or that you had to compensate for manually?

For each skill, write a short honest assessment:

```
## /[skill name]

**Artifact produced:** [What it generated and whether it was right]
**Failures or misfires:** [Anything that went wrong, or "None observed"]
**Gaps:** [What was missing or would have helped, or "None identified"]
```

If a skill performed well with nothing to improve, say so in one sentence and move on. Do not manufacture findings.

---

## Stage 4 — Propose changes

After evaluating all flagged skills, compile a plain English change list. Each proposed change should be specific enough that you — or the agent — could open the SKILL.md file and know exactly what to do.

Format each proposed change as:

```
### Change [N]: [Short title]
**Skill:** /[skill name]
**Type:** [Add section / Remove section / Rewrite section / Change artifact format / Adjust behavior rule / New skill]
**What to change:** [Plain English description of exactly what should be different and why]
```

**Types of changes `/retro` can propose:**

- **Add section** — something new that belongs in the skill but isn't there
- **Remove section** — something in the skill that didn't work or created noise
- **Rewrite section** — something that exists but needs to be clearer, more specific, or restructured
- **Change artifact format** — the structure or content of what the skill produces needs to change
- **Adjust behavior rule** — a rule governing how the agent acts (when to stop, when to ask, what to avoid) needs to change
- **New skill** — something the cycle revealed that doesn't belong in any existing skill and warrants its own command

Order changes by impact — the ones that would most improve the next cycle come first.

---

## Stage 5 — Sign-off prompt

End with this exact block:

---

**Retro complete.**

[N] changes proposed across [N] skills.

To apply these changes:
- Reply **"apply [N]"** to apply a specific change — I'll produce the exact text to add, remove, or replace in that skill file for you to copy in
- Reply **"apply all"** to get the full set of edits in sequence
- Reply **"skip [N]"** to discard a specific change
- Reply **"approved"** if you're satisfied and want to close out the retro

Changes are proposals only — nothing is modified until you approve and apply them yourself.

---

## Applying a change

When the user approves a specific change or asks to apply it, produce the edit in this format:

```
### Applying change [N]: [Title]
**File:** .cursor/skills/[skill-name]/SKILL.md
**Action:** [Add after / Replace / Remove]
**Location:** [Section name or unique phrase to locate the target]

**Text to add / replacement text:**
[Exact markdown text to insert or use as replacement]
```

Be precise about location. "Add after the ## Stage 2 heading" or "Replace the paragraph beginning with 'If the user invokes...'" is actionable. "Add somewhere in the middle" is not.

---

## Rules

- Do not evaluate skills the user didn't flag unless they say "all of them"
- Do not modify any SKILL.md file directly — produce proposals only
- Do not propose changes based on taste or hypothetical improvements — base every change on something that actually happened or clearly didn't work during the cycle
- If the artifacts look fine and the user's description doesn't surface specific problems, say so honestly — a clean retro is a valid outcome
- New skill proposals should include a one-paragraph description of what the skill would do, when it would be invoked, and what artifact it would produce
- The retro is about the skills, not the feature — do not drift into evaluating the quality of the code or the PRD