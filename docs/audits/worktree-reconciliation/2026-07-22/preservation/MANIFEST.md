# Preservation Manifest — `feature/vr-639-640-spec-cli-prereqs` working tree

**Created:** 2026-07-22 · **Source checkout:** `/Users/user1/WebstormProjects/agents-audit` (branch `feature/vr-639-640-spec-cli-prereqs`, HEAD `b6c092b`)

Purpose: capture the only known copy of the uncommitted reconciliation work **before** any integration action. Preservation was fully non-destructive — the source working tree was not switched, staged, reset, or cleaned. Three independent mechanisms preserve the same content.

## At-risk content preserved
- **2 unique CLI integration tests** in `packages/agents-audit/src/cli.integration.test.ts`
  (`--check` + `--dry-run` drift gate still fails; `--force` surfaces relocated invalid file). Confirmed present in the patch (2 matches).
- **`packages/spec/CHANGELOG.md`** [0.4.4] reconciliation narrative.
- **Untracked `packages/spec/src/validator.ts`** (byte-identical to `origin/main`).
- Full tracked delta: **15 files, +179 / −39**.

## Mechanism 1 — Git tag (git-object snapshot of tracked changes)
- **Ref:** `preserve/vr-639-640-worktree-2026-07-22`
- **Commit:** `f5e3e0f16dd7c02a96eb2794aebd6e9789d07eb2` (created via `git stash create`, non-destructive)
- **Verification:** `git diff HEAD preserve/vr-639-640-worktree-2026-07-22 --stat` → `15 files changed, 179 insertions(+), 39 deletions(-)` — matches the live `git diff HEAD` exactly.
- **Recovery (from any checkout of this repo):**
  ```
  git checkout -b recover/vr-639-640 b6c092b
  git cherry-pick --no-commit preserve/vr-639-640-worktree-2026-07-22^..preserve/vr-639-640-worktree-2026-07-22   # or:
  git restore --source=preserve/vr-639-640-worktree-2026-07-22 --worktree -- .
  ```
  (Tag is currently local only; push with `git push origin preserve/vr-639-640-worktree-2026-07-22` to make it remote-durable.)

## Mechanism 2 — Binary-safe patch (committed in-repo, remote-durable once the docs branch is pushed)
- **File:** `tracked-changes.patch` (`git diff HEAD --binary`)
- **SHA-256:** `77513874ea0f5de129ac385cdfa9cd4c2220e89ce684df33a9d1b5c32a449387`
- **Size:** 18747 bytes · **Scope:** tracked modifications only (no untracked files; none were staged).
- **Recovery:**
  ```
  git checkout b6c092b            # the base the patch was cut against
  git apply docs/audits/worktree-reconciliation/2026-07-22/preservation/tracked-changes.patch
  ```

## Mechanism 3 — Verbatim untracked file copy
- **File:** `untracked-validator.ts` (verbatim copy of the untracked `packages/spec/src/validator.ts`)
- **SHA-256:** `fb9a96121d18bbebeddb5ff3df574fd4ac0107cec225564ee4dd4b9c62d74570`
- **Note:** confirmed **identical** to `git show origin/main:packages/spec/src/validator.ts` — also recoverable from `origin/main`.

## Exclusions
- `claudedocs/audit-aa-report.md` — pre-existing untracked prior-audit doc, unrelated to this reconciliation; intentionally not part of this preservation set (not at-risk reconciliation work).
- `docs/` audit artifacts — preserved via the audit-artifact commit (Phase 2), not this patch.
- No secrets, tokens, credentials, or environment values are present in any preserved artifact (verified by inspection; the tracked delta is spec/CLI source + CHANGELOG + lockfile).

## Integrity statement
Source working tree after preservation: still dirty (18 status entries), HEAD still `b6c092b` — unchanged by the preservation operation.
