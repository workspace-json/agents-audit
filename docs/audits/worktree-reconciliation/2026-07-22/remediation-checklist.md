# Remediation Checklist — `agents-audit` Worktree/Linear Reconciliation

**Generated:** 2026-07-22 (audit pass) · **Status:** PROPOSED — do not execute until separate execution authorization is granted.

Rules: work top-to-bottom. **Do not cross a `⛔ VERIFICATION PAUSE` until its checkpoint passes.** Every command below is what a *later* execution agent would run — this audit ran none of them.

---

## Wave 0 — Preservation & Prerequisites  *(do first; parallel-safe)*

### 0.1 — Preserve the primary checkout's uncommitted tree  🔴 CRITICAL
- **Action:** snapshot the dirty working tree of `feature/vr-639-640-spec-cli-prereqs` without merging/pushing/discarding.
- **Evidence:** only copy of the 0.4.4 CHANGELOG split-brain narrative + 2 unique CLI tests + untracked `validator.ts`; a `reset --hard`/`checkout .`/`stash drop` destroys it.
- **Commands (later):**
  ```
  git stash push -u -m "audit-2026-07-22 preservation: vr-639-640 reconciliation" && git stash apply   # keep + restore
  # OR, safer, a durable snapshot:
  git branch preserve/vr-639-640-worktree-2026-07-22
  git add -A && git commit -m "wip: preserve reconciliation snapshot [no-merge]"   # on the preserve branch
  git format-patch -1 -o docs/audits/worktree-reconciliation/2026-07-22/ HEAD
  git bundle create docs/audits/worktree-reconciliation/2026-07-22/vr-639-640-preserve.bundle preserve/vr-639-640-worktree-2026-07-22
  ```
- **Expected:** snapshot commit + `.patch` + `.bundle` exist; original working tree restored intact.
- **Rollback:** `git stash pop` / delete the preserve branch — nothing else touched.
- **Linear after:** none.
- **Unlocks:** feature-branch teardown eligibility (Wave 4).

### 0.2 — Archive-tag the superseded spike
- **Action:** tag `spike/v3d-ajv-validate` before it becomes a deletion candidate.
- **Evidence:** validator productionized into `main` via 0.4.2 (`6c1c01a`); scaffold-cli/pin duplicated in `release/0.4.4`.
- **Command (later):** `git tag archive/spike-v3d-ajv-validate spike/v3d-ajv-validate`
- **Checkpoint:** `git tag -l 'archive/*'` lists it.

### 0.3 — Confirm `release/0.4.4` is the clean vehicle
- **Command (later):** `git -C /private/tmp/agents-audit-release-044 status --short` (expect empty) and `git rev-list --left-right --count origin/main...release/0.4.4` (expect `2  4`).
- **⛔ VERIFICATION PAUSE — G1:** human ratifies `release/0.4.4` as the 0.4.4 vehicle before any integration.

---

## Wave 1 — Foundational Integration (`release/0.4.4` → `main`)

### 1.1 — Refresh `release/0.4.4` from canonical
- **Action:** rebase/merge `origin/main` into `release/0.4.4` (behind 2 — pulls in the PR #17 readme work).
- **Expected conflicts:** low; likely `packages/spec/README.md`, `CHANGELOG.md`.
- **Command (later):** `git checkout release/0.4.4 && git rebase origin/main` (or merge).
- **Rollback:** `git rebase --abort`.

### 1.2 — Port unique content from the preserved feature tree  🔴 CRITICAL
- **Action:** bring into `release/0.4.4`:
  1. the 2 `cli.integration.test.ts` cases (`--check` + `--dry-run` drift gate; `--force` relocated-invalid-file), and
  2. the 0.4.4 CHANGELOG split-brain narrative wording (reconcile with release/0.4.4's existing CHANGELOG).
- **Evidence:** `git diff release/0.4.4 -- packages/agents-audit/src/cli.integration.test.ts` (audit confirmed these are additions absent from release/0.4.4).
- **Expected:** `pnpm --filter agents-audit test` includes and passes the 2 new cases.
- **⛔ VERIFICATION PAUSE — G2/G4:** human confirms port scope and resolves the version-bump disagreement (spec-only vs all-packages → 0.4.4).

### 1.3 — Open PR and pass CI
- **Command (later):** `gh pr create --base main --head release/0.4.4 --title "Release 0.4.4 — reconcile META-101/102 + @workspacejson/cli"`.
- **Validation:** CI green — `pnpm install && pnpm build && pnpm typecheck && pnpm test`. Anti-pattern gates: schema `$id` must be `https://www.workspacejson.dev/schema/v1.json`; CHANGELOG top entry must equal `package.json` version.
- **⛔ VERIFICATION PAUSE:** all required checks green before merge. Never push directly to `main`.

### 1.4 — Merge PR
- **Expected:** `main` advances to 0.4.4; `origin/main:types.ts` now `files: string[]`; schema anchor present.
- **Rollback:** revert the merge PR.

---

## Wave 2 — Dependent Integration

### 2.1 — Merge docs PR #18  *(parallelizable with all of Wave 1)*
- **Evidence:** OPEN, MERGEABLE, base `main`, 1 commit `a91ec1d`.
- **Command (later):** `gh pr merge 18 --squash`.
- **Checkpoint:** PR #18 shows merged.

### 2.2 — Publish 0.4.4 via Release workflow
- **Action:** trigger the fixed-group publish through CI (manual `npm publish` is fenced).
- **Evidence:** memory — Release workflow historically shows red on registry-propagation race but publishes land; do not trust the red alone, verify the registry.
- **⛔ VERIFICATION PAUSE:** confirm 0.4.4 actually on npm before touching Linear.

---

## Wave 3 — Validation & Linear Reconciliation

### 3.1 — Verify published 0.4.4 carries the fixes
- **Command (later):** install the published tarball; assert `types.ts`/schema anchor + `@workspacejson/cli` bin present.
- **Checkpoint:** both META-101 and META-102 fixes observable in the *published* artifact.

### 3.2 — Update Linear (only after 3.1 passes)
- **META-101 / META-102:** post the comments drafted in `repository-reconciliation-report.md`; set status per **G3** (released vs keep-Done).
- **META-97:** comment linking this report.
- **CLI shim issue:** confirm the owning issue (vreko#491 / HAC-75) and close/annotate; create one if none exists.
- **⛔ VERIFICATION PAUSE — G3:** human decides status semantics before any status write.

---

## Wave 4 — Cleanup  *(only after Wave 3 verified)*

### 4.1 — Prune dead worktree admin entries
- **Command (later):** `git worktree prune -v` (removes the 8 missing-dir entries; branches retained).

### 4.2 — Delete integrated local branches (after archive tags)
- **Targets:** `fix/npm-access-command`, `fix/npm-publish-access`, `fix/npm-publisher-preflight`, `fix/npm-token-routing`, `fix/agents-audit-bin-entrypoint`, `fix/ci-pnpm-version`, `release/0.4.2`, `release/v0.3`, `codex-root-workspace-json`.
- **Precondition:** all confirmed `git cherry origin/main <b>` = 0 (done this audit) + optional `git tag archive/<b> <b>`.
- **Command (later):** `git branch -d <b>`.

### 4.3 — Retire spike and feature branches
- **`spike/v3d-ajv-validate`:** after 0.4.4 merged + tag `archive/spike-v3d-ajv-validate` (0.2) → `git branch -D`.
- **`feature/vr-639-640-spec-cli-prereqs`:** **only** after 1.2 port confirmed and 0.1 snapshot exists → teardown.
- **⛔ VERIFICATION PAUSE — G5:** human authorizes each deletion.

### 4.4 — Prune old dangling stashes
- **Targets:** 11 May-2026 dangling commits on merged history. Leave the 4 recent feature-branch ones until 0.1 snapshot is confirmed to cover them.

---

## Local-main housekeeping (non-blocking, any time)
- Fast-forward stale local `main` (behind 21, no unique work): `git checkout main && git pull --ff-only`.

---

## Global guardrails (apply to every step above)
- No direct pushes to `main`; PRs only.
- No manual `npm publish`; CI Release workflow only.
- No branch/worktree/stash deletion before its archive/preservation precondition.
- Re-verify, don't trust, the Release workflow's red status (known false-negative).
- Surface any new impl/spec divergence to the human (META-38); do not auto-resolve.
