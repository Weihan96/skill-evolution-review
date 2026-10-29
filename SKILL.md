---
name: skill-evolution-review
description: Diagnose and improve Codex skills without ad hoc patches. Use whenever Codex creates, updates, reviews, or iterates on a skill, especially alongside or after skill-creator; when a skill failed, overfit a specific case, chose the wrong reasoning order, missed an output/checklist/script, needs trigger tuning, over-triggered from adjacent-domain wording, or when lessons from a task should be distilled into reusable skill behavior. Also use when preparing skill development, releases, or installation. Can be invoked alone to audit an existing skill or with skill-creator as a companion guardrail.
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

## Skill Development, Release and Installation

Keep development, release, and installed content separate. The global `~/.codex/skills` tree (or configured equivalent) holds released installation content, never Git development checkouts. Apply this gate before using, auditing, or preparing to modify a global skill, including this skill itself. Detection is read-only and does not authorize migration, publishing, deletion, or edits.

### Detect and preserve before changing anything

Inspect the selected skill's lexical path and resolved path, its parents and internal links, and `.git` entries of every type: directory, regular file/worktree pointer, symlink, and broken symlink. Check whether Git owns the actual path even without a local `.git`, or whether any link resolves into a development checkout. Any such checkout or marker triggers this workflow; checking only `is_dir('.git')` is insufficient. Broken or unresolvable links require investigation, not a clean-install verdict. Keep inspection scoped to the selected skill; do not batch-migrate neighboring skills.

First identify ownership/provider, source, remotes, HEAD, branch or detached state, staged and unstaged changes, and necessary untracked or ignored content. Inspect linked worktree/common Git directories, submodules, alternate object stores, and other external dependencies when present. Do not reset, clean, remove `.git`, overwrite, or relocate the original during discovery. Redact credentials when reporting remotes.

Before any migration or replacement, preserve the complete old repository and working content outside **all skill-discovery directories**. Preserve history/refs/objects, index state, local modifications, necessary untracked/ignored files, symlink targets and external Git dependencies needed to restore it. A source clone, patch, bundle alone, or copying a `.git` pointer alone is not a complete backup. Verify the backup is independently restorable and record its location; keep originals intact if preservation cannot be proved. Preserve an existing development repository too before any operation that could replace its content. An equal file comparison never waives preservation.

### Choose the development repository and compare actual content

Use the user's specified development root, otherwise `/Users/jiaxinchen/Documents/Projects/Skills`. If that root is missing, ask for an explicit location or permission to create it, and wait. Never create it automatically, guess a substitute, fall back to global skills, or interpret silence/timeouts as consent. If the root exists, cloning a skill subdirectory within the authorized task is normal; root-creation approval is not required again.

Place the repository at `<root>/<skill-name>`. Inspect an existing destination and reuse it when appropriate; never overwrite it. Resolve uncertain source/ownership before cloning. If global and development locations are both repositories, compare **current actual skill content**, including staged/unstaged effects and necessary untracked/ignored files, without comparing `.git` metadata. Compare file bytes, relevant modes, paths and symlink targets; a matching HEAD/SHA does not establish equality. Inspect which untracked files matter; never silently omit uncertain content or publish it automatically.

- If content is identical, no version choice is needed, but preserve both repositories' complete history and changes before migration or replacement.
- If content differs, report added/deleted paths, key content differences and both repositories' HEAD/branch/dirty states. Wait for the user to choose which version to retain or explicitly authorize a combination. Do not overwrite, merge, or choose whichever seems newer. Keep both intact while waiting.

### Work in a genuinely mounted dedicated task

After cloning/reusing source, modification, testing, committing and publishing must occur in a dedicated development task whose actual mounted workspace is that specific repository. Reuse an already verified dedicated task. Report its real task/thread ID, `pwd`, repository root, branch and Git state, and verify its mounted path through task metadata/environment context or the Worktree panel. A shell `workdir` or successful Git switch is not proof of mounting. If mounting cannot be verified, prepare a read-only handoff and wait for a correctly mounted task; do not claim the handoff happened. Do not repurpose the caller's unrelated task or change other threads.

Before each authorized edit batch, apply the user's Git protection rules:

| Initial state | Required action |
|---|---|
| Clean | Proceed within authorized scope. |
| Only unstaged changes, including untracked files | Stage the existing changes as the protected baseline before editing; identify any content that cannot safely be staged and ask rather than discard it. |
| Only staged changes | Preserve that staged baseline. |
| Pre-existing staged + unstaged changes | Stop and ask; give a concise commit recommendation if the staged changes are self-contained. |
| Not a Git repository | Stop and ask. Preserve the user's separate exception for explicitly confirmed personal/global config files; it does not turn an installed skill into a development checkout or waive edit authorization. |

Record the baseline. Mixed state created by protecting a baseline then making this batch's edits is permitted, including follow-up completion/corrections of the same scope. Stop for unknown new changes or a state that no longer matches baseline plus task edits. Do not stage new task edits into the protected baseline without explicit permission. Report overlapping files; do not claim their staged diff is independently committable without verification. Explicit task commit/release authorization permits clearly identified task edits to be staged and committed, but does not authorize consuming unrelated protected changes; isolate them or ask if separation is unsafe. Report the final split: protected staged baseline, task unstaged edits, task commits, and overlaps.

For merge-like operations, retain the user's preflight, conflict-reporting and approval rules, including the narrow chronology-only narrative exception. A dirty target blocks `git merge`; staging/stashing must not bypass its clean preflight. A workflow step here never grants conflict-resolution permission.

### Publish a traceable release, then install the downloaded artifact

Validate and commit the authorized changes in the dedicated task. Tie the release to an immutable commit and, when used, a tag resolving to that commit. Reuse the confirmed source repository and its confirmed visibility. A remote URL alone does not prove permission or public/private visibility. If destination or visibility is unknown, finish local preparation and present the concrete candidate before asking; do not create a repository, push, publish, or make it public by assumption. Long-lived rules do not grant future automatic publishing or deletion rights.

For authorized installation, download the **explicit version's published package** into a fresh temporary directory. Read the release verification reference before packaging, downloading or replacing an installation. Verify the release/commit association, expected content, SHA-256 against a trusted release checksum or equivalent authenticated evidence, safe archive paths/links, and skill validation before replacement. Inspect archive entries before extraction; reject traversal, absolute paths, escaping links, special files and `.git` entries. Verify extracted content as well. A checksum computed only from the download is a fingerprint, not independent verification.

Only that downloaded, verified release package may supply the new global installation. Copying a development tree, using an unpublished local archive, or deleting `.git` to disguise a checkout does not qualify. After preserving the old complete repository/content outside discovery, replace only the selected installation with a staged, validated candidate and retain a rollback path. Verify installed content matches the validated package, version/commit/hash provenance is recorded, no `.git` file/directory/link exists at any depth, and neither the installation nor its internal links/ancestors resolve into a checkout. If any check fails, retain/restore the old version and report failure; do not delete the backup or claim success.

Official/system and third-party managed skills retain their ownership and update boundaries. Report detected checkout problems without moving or rewriting managed content absent explicit authorization and a compatible provider update path. Prefer a personal companion for personal rules.

**Self-bootstrap order:** Complete this skill's own development → authorized release → verified download → installation → post-install verification cycle first. Then re-read the newly installed instructions before handling `git-graph-browser`, and only within that task's authorization. If release destination/visibility is pending, stop at local preparation: no global replacement and no edits to `git-graph-browser`.

## Reference Routing

Read only the relevant reference files:

| Situation | Read |
|---|---|
| Diagnosing why a skill failed, classifying failure modes, choosing validation | [failure-diagnosis.md](references/failure-diagnosis.md) |
| Deciding where a lesson belongs and avoiding ad hoc overfitting | [placement-rules.md](references/placement-rules.md) |
| Preparing a release, preserving a checkout, or verifying an installation | [release-verification.md](references/release-verification.md) |

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
