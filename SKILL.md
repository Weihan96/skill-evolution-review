---
name: skill-evolution-review
description: Diagnose and improve Codex skills without ad hoc patches. Use whenever Codex creates, updates, reviews, or iterates on a skill, especially alongside or after skill-creator; when a skill failed, overfit a specific case, chose the wrong reasoning order, missed an output/checklist/script, needs trigger tuning, over-triggered from adjacent-domain wording, or when lessons from a task should be distilled into reusable skill behavior. Can be invoked alone to audit an existing skill or with skill-creator as a companion guardrail.
---

# Skill Evolution Review

Use this as a companion to `skill-creator`, not as a replacement. Its job is to diagnose why a skill failed or needs improvement, then decide where the fix belongs so the skill generalizes instead of accumulating one-off patches.

## Skill Evolution Loop

Run this loop before editing a skill:

| Gate | Required answer | Details |
|---|---|---|
| Failure Evidence | What did the skill do, what should it have done, and what artifact proves the gap? | [failure-diagnosis.md](references/failure-diagnosis.md) |
| Failure Class | Is this missing knowledge, wrong reasoning order, weak trigger, overbroad trigger, missing output structure, missing validation, missing automation, or review-enforcement gap? | [failure-diagnosis.md](references/failure-diagnosis.md) |
| Generality Test | Could the same failure recur in a different domain or only in this exact case? | [placement-rules.md](references/placement-rules.md) |
| Placement Decision | Should the fix go in `SKILL.md`, a reference, output format, checklist, script, `agents/openai.yaml`, or downstream agent prompt? | [placement-rules.md](references/placement-rules.md) |
| Anti-Ad-Hoc Check | Does the proposed fix patch only the last incident, or improve the skill's reusable decision behavior? | [placement-rules.md](references/placement-rules.md) |
| Validation | How will the updated skill be validated: quick validation, self-check, forward-test, or review-agent enforcement? | [failure-diagnosis.md](references/failure-diagnosis.md) |

If the problem is a systemic reasoning-order failure, promote the fix into the skill's main `SKILL.md` workflow. Do not hide required decision loops in optional references.

## Reference Routing

Read only the relevant reference files:

| Situation | Read |
|---|---|
| Diagnosing why a skill failed, classifying failure modes, choosing validation | [failure-diagnosis.md](references/failure-diagnosis.md) |
| Deciding where a lesson belongs and avoiding ad hoc overfitting | [placement-rules.md](references/placement-rules.md) |

## Placement Rules

- Put invariant decision loops, trigger-critical behavior, and required output structure in `SKILL.md`.
- Put trigger overreach fixes in frontmatter and core posture: define the skill by its own positive inputs/outputs, not by adjacent-domain terms, neighboring skill names, or negated boundaries.
- Put domain-specific details, examples, long tables, and specialized checklists in `references/`.
- Put repeated mechanical or fragile operations in `scripts/`.
- Put UI-facing display name, short description, and default prompt in `agents/openai.yaml`.
- Put enforcement requirements in downstream review prompts or review checklists when another agent must reject bad output.
- Update a skill's frontmatter `description` when the issue is that the skill did not trigger or triggered too broadly.

## Output Format

Use this concise format unless the user asks for implementation only:

```md
Failure:

Diagnosis:
| Failure mode | Evidence | Fix level |
|---|---|---|

Generalized lesson:

Skill changes:
- `SKILL.md`:
- `references/`:
- `scripts/`:
- `agents/openai.yaml`:
- downstream review/enforcement:

Anti-ad-hoc check:
- Why this is not just patching the last incident:

Validation:
- ...
```

## Guardrail

Do not edit official/system skills to preserve personal lessons unless the user explicitly asks. Prefer a personal companion skill, project policy, or downstream review guardrail so official updates do not overwrite local learning.
