# Release and Installation Verification

Read this when preparing a release or replacing a selected global skill. The mandatory gates and permissions live in `SKILL.md`; this reference describes evidence to collect. It is a procedure, not an automatic installer or authorization to run it.

## Read-only inventory and recovery evidence

Record the selected lexical/real paths and ownership, Git root, Git/common directories, source remotes (redacted), HEAD, branch/detached state, status including all untracked paths, and staged/unstaged diffs. Account for ignored runtime/config/assets that are necessary to recover the working skill. Do not publish secrets or incidental local files just because they need preservation.

Use a filesystem walk that does not blindly follow symlinks: inspect links with `lstat`/`readlink`, including dangling `.git` links. Resolve links deliberately, detect cycles, and check ancestor repository ownership. Inventory nested repositories and external Git storage before backup. For linked worktrees, a `.git` file names storage outside the skill; copying only the visible directory loses history/index state. A clone or Git bundle also omits working changes and necessary untracked content.

Choose a backup location outside every configured skill-discovery root and outside directories scheduled for replacement. Preserve both the visible content and all storage needed for independent recovery, including the index and external Git dependencies. Verify file manifests and repository objects/refs and demonstrate the saved working/index state can be reconstructed without relying on paths that will be replaced. `git fsck --full` is useful object-integrity evidence, but is not proof that untracked files or the index were preserved. Never remove the only complete copy; retain backup locations in the handoff.

For two repositories, build manifests of current actual skill content, excluding `.git` metadata at every depth. Include relative paths, object types, relevant modes, file hashes and symlink targets; inspect necessary ignored/untracked files explicitly. Compare path additions/deletions and material text/binary differences. Do not use commit equality or a clean tracked diff as a substitute. Equal manifests eliminate the content-choice question, not the requirement to preserve differing histories or staged states.

## Release candidate evidence

Before external publication, record:

- Dedicated task ID and actual mounted repository, `pwd`, branch and status; protected baseline and task changes/overlap.
- Confirmed source/destination, public/private visibility and the authorization for this publication. If unknown, prepare the local candidate and wait; no guessed repository creation or visibility change.
- Full commit ID and optional tag resolved to that commit; validation results and a reviewed package inclusion list.
- Package layout, expected files/modes, source-content manifest and exclusions. Include all referenced runtime resources, omit Git storage and incidental local files. Never blindly package a dirty tree.

Use the commit as the source identity rather than writing a self-referential commit ID into the commit itself. Record the final commit, release URL, version, archive checksum and manifest in release metadata or an external installation receipt. Keep the receipt outside the payload, so installed files can exactly match the package. Report only observed release URLs, hashes and installation outcomes.

## Download and validate before extraction/replacement

1. Resolve the explicit published version to its full commit and package URL from the confirmed source. For private sources, use authorized authenticated access without exposing tokens. Fetch into a fresh temporary directory; keep the current installation untouched.
2. Calculate the archive SHA-256 and compare with a trusted published checksum or equivalent authenticated source evidence. If the provider supplies no independent checksum, compare extracted files with a trusted manifest from the exact source commit as well as recording the download hash. Stop on mismatch or an unverifiable source association. A self-computed hash alone proves neither authenticity nor correct version.
3. Inspect archive entries **before** extraction. Reject absolute/drive-qualified paths, `..` traversal, paths escaping the extraction root, duplicate/normalization-colliding paths, device/FIFO/special entries, and any `.git` path component (file, directory, hardlink or symlink, including aliases on the destination filesystem). Account for the destination's case and Unicode normalization. Do not run package scripts to inspect an archive.
4. Check symlink and hardlink targets, including chained links and link-then-write entries. Reject escaping, dangling, cyclic or checkout-directed links. If safe extraction cannot be established with the available extractor, reject the package or request a regular-files-only release; do not experiment in the global directory. Apply limits appropriate to the package's expected extracted size/count.
5. Extract into isolation with safe semantics. Inspect the extracted tree again without skipping hidden files or following unknown links. Confirm expected skill root/layout, no extra payload, no `.git` entries, no resolved checkout paths, and agreement with the trusted source manifest. Check referenced files exist. Run the available skill-creator `quick_validate.py` against this extracted skill; run any skill-specific checks required by its actual content. Treat validation failure as a failed candidate.

## Install, verify and recover

With installation authorized and the old version fully preserved, stage the candidate from the **downloaded verified artifact**, never from a checkout or an unpublished local archive. Keep the candidate outside skill discovery until the replacement, so duplicate skills are not discovered. Recheck that source installation and development state have not changed since inventory; unknown changes require renewed preservation/comparison, not overwrite.

Replace only the selected skill, retaining a recoverable old version outside discovery throughout. After replacement:

- Compare every installed payload path/type/mode/hash/link target against the validated extracted package, and detect unexpected extra files.
- Record the exact source, version, full commit, archive SHA-256, verification method and backup location in an external receipt; ensure version provenance is still available after temporary files are removed.
- Scan with link-aware filesystem inspection for every `.git` entry, including hidden/nested/dangling entries. Inspect installation-root and internal links and resolved parent paths for checkout ownership. No `.git` marker alone is not sufficient proof.
- Re-run skill validation at the installed path and confirm its referenced resources are present. Report the checked scope and results; do not infer success merely from copying files.

If pre-replacement validation fails, leave the old version active. If post-replacement checks fail, restore the preserved old version, keep the failed candidate isolated for diagnosis, verify the restored content/state and report rollback. A legacy checkout restored during rollback remains a known unresolved migration issue; it is not a compliant new installation. If restoration itself fails, report the exact state and backup location without claiming success. Never delete recovery copies automatically.

For the one-time initial migration, successful installation of `skill-evolution-review` and re-reading that installation precede any separately authorized `git-graph-browser` migration. Stop after this skill if that is the authorized scope. This historical sequence is not a permanent cross-skill prerequisite; subsequent releases and unrelated skills follow their own scope and authorization.
