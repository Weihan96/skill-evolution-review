# Failure Diagnosis

Use this to classify why a skill failed before deciding how to update it.

## Failure Modes

| Failure mode | Symptom | Usual fix |
|---|---|---|
| Missing domain knowledge | The agent did not know a domain rule, schema, policy, API, or exception | Add or update a reference file |
| Wrong reasoning order | The agent knew the principles but applied them too late or skipped a necessary gate | Promote a decision loop into `SKILL.md` |
| Weak trigger | The skill was not selected when it should have been | Rewrite frontmatter `description`; update `agents/openai.yaml` default prompt only for UI help |
| Overbroad trigger | The skill was selected or steered by adjacent-domain terms, named neighboring skills, broad examples, or negated boundary wording outside its actual job | Rewrite frontmatter and core posture around positive input/output boundaries; remove attractor terms and neighboring-skill names unless they are required triggers |
| Missing output structure | The answer omitted a required comparison, evidence field, checklist, or handoff | Update `SKILL.md` output format |
| Missing validation | The skill produced plausible output with no self-check | Add validation steps, checklist, or forward-test guidance |
| Mechanical repetition | The agent repeatedly rewrites fragile commands or code | Add a script |
| Review enforcement gap | The skill says what to do, but another agent can accept bad output | Update review checklist or downstream agent prompt |
| Overfit/ad hoc patch | A new rule solves only the last incident and would not generalize | Extract the invariant and put the case-specific detail in a reference |
| Stale project-policy trust | The skill treats current repo docs or structure as automatically correct | Add discovery/ranking rules or a current-reality gate |

## Evidence To Capture

Before editing, identify:

- the user request or task that triggered the failure,
- the skill output or behavior that was wrong,
- the expected behavior,
- whether the failure came from trigger selection, reasoning order, missing data, missing validation, or downstream review,
- whether the trigger problem was a false negative, false positive, or post-trigger reasoning drift,
- whether wording in frontmatter or core posture used adjacent-domain terms, neighboring skill names, negated boundaries, or examples that attract the wrong tasks,
- whether a different domain could produce the same failure pattern.

## Validation Levels

| Change type | Validation |
|---|---|
| Frontmatter or structure change | Run `quick_validate.py` |
| New script | Run the script on a representative input |
| New reasoning loop | Test against the incident and at least one different-shaped example when practical |
| Complex skill revision | Forward-test with a subagent using a realistic request and without leaking the intended answer |
| Review-enforcement update | Verify the review checklist would reject the old bad output |
