# Placement Rules

Use this to decide where a lesson belongs inside or around a skill.

## Core Rule

If the same failure could recur in a different domain, fix the skill's general decision behavior. If the failure depends on one domain's facts, put the details in a reference.

## Where To Put The Fix

| Lesson type | Put it in | Why |
|---|---|---|
| Always-required decision sequence | `SKILL.md` main workflow | The agent must see it whenever the skill triggers |
| Trigger condition or companion-skill behavior | Frontmatter `description` | Skill selection depends only on metadata before body load |
| Trigger overreach or scope-attractor drift | Frontmatter `description` and `SKILL.md` core posture | Selection depends on metadata, and body wording can still steer reasoning after trigger |
| Required answer fields | `SKILL.md` output format | Prevents repeated omissions |
| Domain-specific facts, examples, policy tables | `references/` | Keeps `SKILL.md` lean while preserving detail |
| Long checklist for a specific surface | `references/` | Load only when relevant |
| Repeated fragile operation | `scripts/` | Mechanical reliability beats prose |
| UI chip/default prompt | `agents/openai.yaml` | Helps users invoke the skill but does not control triggering |
| Downstream accept/reject behavior | Review checklist or review-agent prompt | A skill cannot enforce quality alone if another agent approves output |
| Project-specific lesson | Project `AGENTS.md` or project policy | Avoid polluting a general skill with local taste |
| Personal meta-learning from official skill use | Personal companion skill | Avoid edits that official updates may overwrite |

## Anti-Ad-Hoc Check

Before editing, ask:

- Does this fix only name the latest incident, or does it improve a reusable decision pattern?
- Would the same rule help if the domain were different?
- Is the case-specific detail separated from the general protocol?
- Is the new rule placed where it will actually be loaded before the failure repeats?
- Does the fix remove broad attractor terms and define the skill by its own input/output boundary instead of naming adjacent skills or saying what it is not?
- Does a downstream review agent need to enforce the new behavior?

## Promotion Rule

Promote reference material into `SKILL.md` when:

- it is required for nearly every use,
- it defines the order of reasoning,
- it prevents a known systematic bias,
- it changes the skill's default recommendation behavior,
- or missing it would make the skill unsafe or misleading.

Demote from `SKILL.md` into references when:

- it is domain-specific,
- it is long,
- it only applies to a subset of use cases,
- or it is an example rather than an invariant.
