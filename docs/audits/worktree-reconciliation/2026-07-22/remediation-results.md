# Remediation Results — Preservation & Integration Pass (2026-07-22)

**Scope:** bounded first remediation pass authorized against `docs/audits/worktree-reconciliation/2026-07-22/`.
**Boundary honored:** no merge, no publish, no Done, no deletions, no stash drops, no force-push, no history rewrite, no unreachable-object removal. META-38 followed (impl/spec divergence surfaced, not auto-resolved).

---

## Drift Check

**Classification: `SAFE_DRIFT`.** The audit baseline held; the one refinement (release/0.4.4 already carrying a truthful CHANGELOG entry) reduced work rather than invalidating any operation.

| Check | Expected (audit) | Observed | Result |
|---|---|---|---|
| Primary HEAD | `b6c092b` (feature/vr-639-640) | `b6c092b` | ✅ |
| Dirty unique work present | 2 CLI tests + CHANGELOG + validator.ts | all present | ✅ |
| META-101/102 impl on release/0.4.4 | present | `types.ts:101 files: string[]`, schema anchor ×2, cli pkg, validator.ts | ✅ |
| 2 CLI tests unique to dirty tree | yes | 0 matches in release/0.4.4 | ✅ |
| CHANGELOG material | unique | release/0.4.4 already has an accurate `[0.4.4]` entry; feature narrative adds unverifiable claims | ⚠️ refined |
| PR for release/0.4.4 | none | none | ✅ |
| META-101/102 status | Done | Done | ✅ |
| New worktrees/branches/commits invalidating audit | none | none | ✅ |

**Refinement discovered:** the "two unique CLI tests" are **not standalone** — they are regression tests for two *uncommitted `cli.ts` behavior changes* (see Conflict Decisions). The original audit characterized them as tests-only; that was incomplete. Surfaced here, not silently resolved.

---

## Preservation Artifacts

The primary checkout working tree (`feature/vr-639-640-spec-cli-prereqs`) was preserved by three independent, non-destructive mechanisms. No `reset --hard`/`checkout .`/`clean`/`stash drop` was used; the source tree remained dirty and on `b6c092b` throughout.

| Artifact | Location | Verification | Scope |
|---|---|---|---|
| **Git tag** `preserve/vr-639-640-worktree-2026-07-22` | commit `f5e3e0f` (via `git stash create`) | `git diff HEAD <tag> --stat` = `15 files, +179 −39`, matches live `git diff HEAD` | tracked changes (git object, GC-safe) |
| **Patch** `tracked-changes.patch` | `docs/audits/worktree-reconciliation/2026-07-22/preservation/` (committed `9619662`, pushed) | SHA-256 `77513874…449387`; 18747 B; `git apply --stat` = 15 files; contains both unique tests (grep=2) | tracked changes (text, remote-durable) |
| **Verbatim copy** `untracked-validator.ts` | same dir (committed `9619662`) | SHA-256 `fb9a9612…d74570`; `diff` vs `origin/main:…/validator.ts` = identical | the sole unique untracked file |
| **Manifest** `preservation/MANIFEST.md` | same dir (committed `9619662`) | recovery commands + hashes recorded | index of the above |

**Recovery (no dependence on the current worktree path):**
- `git restore --source=preserve/vr-639-640-worktree-2026-07-22 --worktree -- .`  (tracked), or
- `git checkout b6c092b && git apply docs/audits/worktree-reconciliation/2026-07-22/preservation/tracked-changes.patch`.

**Exclusions:** `claudedocs/audit-aa-report.md` (pre-existing unrelated prior-audit doc); `docs/` audit artifacts (preserved via the audit commit). **No secrets/credentials/env values** in any artifact (verified).
**Note:** the git tag is currently local-only; the committed patch is the remote-durable copy. Push the tag with `git push origin preserve/vr-639-640-worktree-2026-07-22` if a remote git-object copy is also wanted.

---

## Audit Artifact Commit

- **Branch:** `docs/reconciliation-audit-2026-07-22` (off `origin/main` `5a0e37e`, in an isolated worktree so the dirty primary checkout was untouched).
- **Commit:** `9619662` — `docs(audit): record 2026-07-22 worktree/Linear reconciliation audit + preservation`.
- **Contents (docs-only, 6 files):** the 3 audit artifacts + `preservation/{tracked-changes.patch, untracked-validator.ts, MANIFEST.md}`. `git diff origin/main --name-only` = docs-only. No product source.
- **Pre-commit checks:** JSON valid; secret scan clean (only match was the `fix/npm-token-routing` *branch name*); repo/date references correct.
- **Pushed:** `origin/docs/reconciliation-audit-2026-07-22`.
- **Entry into canonical:** via PR (repo is PR-only). See Remaining Approval Gates.

---

## Ported Changes

| Item | Decision | Rationale |
|---|---|---|
| 2 unique CLI integration tests → release/0.4.4 | **NOT ported** | Proven to fail against release/0.4.4 (they test uncommitted `cli.ts` behavior). Porting them alone injects failing tests; porting the coupled `cli.ts` impl is outside the approved scope (tests + CHANGELOG only). See Conflict Decisions → `REQUIRES_HUMAN_DECISION`. |
| `0.4.4` CHANGELOG narrative → release/0.4.4 | **No change needed** | release/0.4.4 already carries a truthful, sufficient `[0.4.4]` entry covering the VR-639/640 reconciliation. The feature branch's longer narrative adds claims validation cannot support. See Conflict Decisions. |

**Net effect on `release/0.4.4`: zero commits.** It was already the clean, rebased-on-main integration vehicle; nothing needed porting into it, and nothing portable-in-scope was missing.

---

## Conflict Decisions

**1. CLI tests are coupled to unshipped implementation (`REQUIRES_HUMAN_DECISION`).**
The feature working tree's `cli.ts` (`git diff HEAD -- packages/agents-audit/src/cli.ts`) makes two behavior changes the two tests lock in:
- reorders `--check` + `--dry-run` so the drift gate fires (exit 1) instead of the dry-run branch winning (exit 0);
- adds an `invalidFileMoved` recovery message branch.
release/0.4.4's `cli.ts` has neither (`if (options.dryRun)` is still first; no `invalidFileMoved`).
**Empirical proof** (tests inserted into release/0.4.4, run, then reverted to pristine):
```
❯ still fails the drift gate when --check is combined with --dry-run
    → expected +0 to be 1
❯ surfaces the relocated invalid file when --force recovers a fresh generate
    → expected 'Generated /repo/.agents/workspace.json' to contain '/repo/.agents/workspace.json.invalid.…'
Test Files 1 failed | 10 passed · Tests 2 failed | 60 passed
```
**Decision:** do not port the tests, and do not port the coupled `cli.ts` impl (out of approved scope, and it is a genuine product/semantics change). Both behaviors + tests are fully preserved (tag + patch) and ready for a follow-up authorization. This is exactly the class META-38 says to surface, not resolve.

**2. CHANGELOG — target already truthful (`no_change_needed`).**
release/0.4.4's `packages/spec/CHANGELOG.md` `[0.4.4] - Unreleased` already states: "Reconciled the strict packaged-schema validator with the VR-639/640 contract fixes that had diverged across earlier release branches," plus the fileIndex / coChange / version bullets. The feature branch's narrative additionally claims "0.4.2/0.4.3 were published from `spike/v3d-ajv-validate`" — imprecise (0.4.2 was published from `release/0.4.2` PR #10; the validator merely *originated* on spike). Per "do not preserve claims validation cannot support," that wording was **excluded**. No overwrite of release/0.4.4's history occurred.

---

## Validation Results

Run 2026-07-22 in the `release/0.4.4` worktree at head `3a77e1b` (pristine — the empirical test insert above was reverted before validation):

| Command | Result |
|---|---|
| `pnpm -r build` | ✅ pass (all packages) |
| `pnpm -r typecheck` | ✅ pass — spec, rules, cli, agents-audit (`tsc --noEmit` each "Done") |
| `pnpm -r test` | ✅ **275 passed, 0 failed** — spec 36, cli 6, rules 173, agents-audit 60 |

Acceptance-criterion spot checks on the built branch:
- META-101: `packages/spec/src/types.ts:101` → `files: string[]` (set), not tuple.
- META-102: `packages/spec/schema/v1.json` → "repository-root-relative" anchor present (2 sites).

No failures introduced; no checks skipped. The `.npmrc` `${NPM_TOKEN}` warning is a benign local-env message, unrelated to correctness. Not run this pass: packaged-tarball publish verification (belongs to the Release workflow) and secret-scanning CI (GitHub-side).

---

## Pull Request

- **PR #19** — `release/0.4.4` → `main` — OPEN, `MERGEABLE`, not draft.
  https://github.com/workspace-json/agents-audit/pull/19
- Body: states it reconciles previously-unshipped work; lists validation results; links META-101/102/VR-639/VR-640 and META-31; explicitly notes 0.4.4 is **not** released and the 2 deferred tests. **No auto-close keywords** (merge ≠ release), confirmed by diff scan.
- Diff review: 28 files, all in-scope (spec fixes, `@workspacejson/cli`, changelogs, versions, lockfile). No unrelated changes, no reversions, no secrets, no private identifiers.
- Branch is **2 commits behind `origin/main`** (docs-only, PR #17) — use GitHub "Update branch" before merge.
- Not merged (per authorization).

Audit-docs branch `docs/reconciliation-audit-2026-07-22` is pushed; its PR is a remaining step (see gates).

---

## Linear Updates

| Issue | Before | After | Actions |
|---|---|---|---|
| META-101 | Done (completed) | **In Review** (started, `completedAt: null`) | status change + evidence comment `030bed60` + PR #19 link `726e547b` |
| META-102 | Done (completed) | **In Review** (started, `completedAt: null`) | status change + evidence comment `30ba36d7` + PR #19 link `6e165a69` |

Each comment records: the published-0.4.3 gap, the branch/PR carrying the fix, the specific file evidence, the validation, the remaining Done condition (merge + publish + verify), and a META-31 cross-reference. **Neither issue was marked Done.** "In Review" chosen because the team workflow has no "Merged" state (Triage/Scoped/Todo/In Progress/In Review/Done/Canceled/Duplicate) — documented as a concrete case for META-31.

Not modified: META-97 (census anchor) — the audit proposed a comment there, but posting it was outside this pass's enumerated Linear scope (META-101/102 only). Left for the next authorization.

---

## Deviations From Audit

1. **CHANGELOG port became a no-op.** The audit assumed the 0.4.4 narrative was missing from release/0.4.4; it is already present and truthful. Reconciliation = confirming that and excluding the feature branch's unverifiable claims.
2. **CLI tests not ported.** The audit listed them as portable "tests only." Execution proved they are coupled to unshipped `cli.ts` behavior; porting was therefore out of scope. Reclassified to `REQUIRES_HUMAN_DECISION`.
3. **release/0.4.4 not refreshed onto origin/main.** Left 2-behind (docs-only) rather than performing an unnecessary history op; flagged for "Update branch" at merge time. PR is `MERGEABLE` regardless.
4. **Personal absolute path in committed audit docs.** `/Users/user1/...` appears in `repository-reconciliation.json` and `preservation/MANIFEST.md` as legitimate provenance evidence. Not scrubbed (would falsify the record) — but recommend a scrub pass before this repo goes public as the `workspacejson` standard.

---

## Remaining Approval Gates

- **G1 — vehicle:** resolved by this authorization (release/0.4.4 is the vehicle).
- **G2 — port the 2 tests + coupled `cli.ts` behaviors:** **UNRESOLVED — needs decision.** Approve porting the two `cli.ts` hunks (drift-gate reorder + `invalidFileMoved` messaging) *and* their tests as a follow-up, or rule those behaviors out of 0.4.4. Preserved and ready either way.
- **G3 — Done semantics:** applied as directed (Done→In Review). Return to Done only post-publish + verify.
- **G4 — fixed-group versioning:** release/0.4.4 already bumps all packages to 0.4.4 (self-consistent); the feature tree's spec-only bump is superseded. No action taken; confirm at release time.
- **G5 — deletions:** none performed; deferred (see below).
- **PR merges:** PR #19 (release) and a PR for `docs/reconciliation-audit-2026-07-22` both await human authorization. `gh pr create --base main --head docs/reconciliation-audit-2026-07-22` opens the docs PR.

---

## Explicitly Deferred Cleanup (nothing deleted this pass)

| Object | State | Why deferred |
|---|---|---|
| **Worktrees** — 8 prunable admin entries (`agents-audit-npm-*`, `-release-042`, `-v3d-ajv`, `-v3d-baseline`, `hac-181-prechange`), + `-bin-entrypoint`, `-readme-final`, `-release-044`, primary | all left in place | `git worktree prune` deferred to post-merge cleanup authorization |
| **Local branches** — `fix/npm-access-command`, `fix/npm-publish-access`, `fix/npm-publisher-preflight`, `fix/npm-token-routing`, `fix/agents-audit-bin-entrypoint`, `fix/ci-pnpm-version`, `release/0.4.2`, `release/v0.3`, `codex-root-workspace-json` (all integrated) | not deleted | cleanup requires separate authorization |
| **`spike/v3d-ajv-validate`** | not deleted | retire only after 0.4.4 merges + archive tag |
| **`feature/vr-639-640-spec-cli-prereqs`** + its dirty tree | not deleted, still dirty | holds the deferred G2 work; preserved but not yet integrated |
| **Stashes** | `git stash list` empty; 15 dangling stash-shaped commits (incl. 4 recent feature-branch WIP) | none dropped; unreachable-object removal explicitly out of scope |
| **`preserve/…` tag** | created | intentionally retained |

---

## Final State

| Item | Value |
|---|---|
| Canonical branch commit | `origin/main` `5a0e37e` — **unchanged** |
| `release/0.4.4` commit | `3a77e1b` — **unchanged** (zero commits added) |
| PR status | **#19 OPEN / MERGEABLE** (not merged); docs PR pending |
| Validation status | build ✅ · typecheck ✅ · test **275/275** ✅ |
| META-101 | **In Review** (was Done) |
| META-102 | **In Review** (was Done) |
| Remaining dirty worktree | primary `feature/vr-639-640` still dirty (18 entries), HEAD `b6c092b` — **untouched** |
| Preservation status | ✅ triple-preserved (tag `f5e3e0f` + committed patch + verbatim copy) |
| Audit artifact status | ✅ committed `9619662`, pushed `origin/docs/reconciliation-audit-2026-07-22` |
| Remaining worktrees | 12 (unchanged) |
| Branches with unique unmerged work | `feature/vr-639-640` (4), `release/0.4.4` (4, in PR #19), `spike/v3d-ajv-validate` (3), `docs/readme-clarity-final` (1, PR #18), `docs/reconciliation-audit-2026-07-22` (1) |
| Any unique work at risk? | **No.** The only at-risk item (feature dirty tree) is triple-preserved. |

---

## Recommendation for Next Authorization

### `REQUIRES_HUMAN_DECISION`

Two decisions block a clean close:

1. **G2 — the two CLI tests + their `cli.ts` behaviors.** They cannot land without an implementation/semantics change (drift-gate reorder for `--check --dry-run`, and `invalidFileMoved` recovery messaging) that exceeds this pass's approved scope. Decide: (a) authorize porting both hunks + tests into a follow-up PR, or (b) rule the behaviors out of 0.4.4. Work is preserved either way.
2. **Merge/publish path for PR #19.** Validated and mergeable, but merging + publishing 0.4.4 was explicitly out of scope. This is the step that will actually let META-101/102 return to Done.

Everything mechanically safe in this pass is done: preservation, audit commit, PR #19, validation evidence, and the Linear correction. **Do not merge, publish, delete, or port implementation until the above is authorized.**

---

# Release Execution Addendum — 2026-07-23

This section is **appended** (the original evidentiary record above is unchanged). It records the governed `0.4.4` merge, publish, verification, and Linear reconciliation pass that followed, and supersedes the previous pass's `REQUIRES_HUMAN_DECISION` recommendation.

Pre-merge drift: **`SAFE_DRIFT`** — PR #19 MERGEABLE/CLEAN, CI `test (20)`+`test (22)` green on the merge ref, excluded CLI behaviors absent, versions correct, META-101/102 still In Review, preservation intact.

## Release Merge

- **PR:** #19 (`release/0.4.4` → `main`).
- **Merge commit:** `d0a19f6` (merge commit; repo's standard "Merge pull request" policy, matching history #10–#17).
- **Merge-candidate validation:** locally merged `origin/main` into `release/0.4.4` (no-commit) → `pnpm -r build` ✅, `pnpm -r typecheck` ✅, `pnpm -r test` **275 passed / 0 failed** (spec 36, cli 6, rules 173, agents-audit 60); merge aborted to keep the branch pristine. GitHub required checks `test (20)`/`test (22)` also green on the PR merge ref.
- Post-merge `origin/main` carries: spec `0.4.4`, `types.ts files: string[]`, schema anchor ×2, `@workspacejson/cli` present, excluded CLI display messaging **absent**.

## Published Release

- **Version:** `@workspacejson/spec@0.4.4`, `@workspacejson/rules@0.4.4`, `agents-audit@0.4.4`.
- **Not published:** `@workspacejson/cli` (`private: true`, `0.0.1`) — in-repo scaffold only; correctly skipped by changesets.
- **Tag:** `v0.4.4` (annotated, `7d562fa`) on `d0a19f6`.
- **Workflow:** Release run `29967929171` via `workflow_dispatch` on `main` — the repo's established path (the tag trigger did not fire; every prior release incl. 0.4.3 used dispatch, and no `v0.4.3` tag ever existed). The `Publish packages` step **succeeded**; changesets reported `packages published successfully: agents-audit@0.4.4, @workspacejson/rules@0.4.4, @workspacejson/spec@0.4.4`.
- **Known false-red:** the post-publish `Verify published packages from the registry` step failed on the registry-propagation race (documented flake); the publish itself succeeded and propagation completed within ~1 min. `latest=0.4.4` confirmed via independent HTTPS reads.
- **Integrity (published tarball shasums):** spec `8a3df59c…9f65`, rules `f77e1d60…0ebb`, agents-audit `263f9b84…1ad5` — all matched on download.

## Published-Artifact Verification

Clean-environment procedure: downloaded each `0.4.4` tarball, verified shasum, extracted; installed `ajv` into a throwaway app and imported the **published** `@workspacejson/spec` dist.

- Versions: all three report `0.4.4`. ✅
- **META-101:** published `schema/v1.json` `coChange.items.files` documented "Unordered pair (set semantics — position is NOT meaningful; join by membership, not index)", `minItems:2/maxItems:2`; `types.ts` → `files: string[]`. Functional smoke: a pair validates identically in `[a,b]` and `[b,a]`, a 3-file entry is rejected, `validate({})` rejected. ✅
- **META-102:** "repository-root-relative POSIX path" anchor at 2 sites in published `schema/v1.json`; strict AJV validator loads and enforces at runtime. ✅
- **Contents clean:** no `/Users/`, `node_modules/`, dotenv, npmrc, tokens, or keys in any tarball; spec = 15 files (matches `fileCount`).
- **Excluded behavior absent:** published `agents-audit` dist has neither excluded display string ("moved aside", "was not recovered"); `invalidFileMoved` appears only as the pre-existing generate result field (5 refs already in 0.4.3), not the deferred CLI messaging.

## Linear Final State

- **META-101:** In Review → **Done** (`completedAt 2026-07-23T00:10`), closing comment `b37f6f89` with published-artifact evidence + PR #19 link.
- **META-102:** In Review → **Done** (`completedAt 2026-07-23T00:10`), closing comment `e12654ab`.
- Done applied **only after** published-artifact verification (In Review → released → Done). Both comments cite **META-31** as the concrete Merged-vs-Done case.
- **META-157** (new, Backlog, Medium, not started): the deferred CLI behaviors + tests follow-up; PR #19 linked.

## Documentation Merge

- **PR #20** (`docs/reconciliation-audit-2026-07-22` → `main`): docs-only, confirmed. This addendum is committed to that branch, then PR #20 is merged as the final governed step of this pass (merge commit on `main`).

## Preserved Deferred Work

Nothing lost; the deferred CLI work is triple-preserved and tracked in **META-157**:
- CLI implementation changes: `cli.ts` drift-gate reorder + `invalidFileMoved` messaging.
- Coupled tests: two `cli.integration.test.ts` cases.
- Preservation tag `preserve/vr-639-640-worktree-2026-07-22` (`f5e3e0f`); patch `preservation/tracked-changes.patch` (SHA-256 `77513874…449387`); manifest `preservation/MANIFEST.md`; verbatim `preservation/untracked-validator.ts`.
- Recovery: `git restore --source=preserve/vr-639-640-worktree-2026-07-22 --worktree -- .` or `git checkout b6c092b && git apply …/tracked-changes.patch`.

## Cleanup Readiness

Cleanup is **prepared, not executed.** Per-target status:

| Target | Unique work? | Integrated? | Preserved? | Dependency remaining? | Eligible? | Precondition |
|---|---|---|---|---|---|---|
| `release/0.4.4` branch + `/private/tmp/agents-audit-release-044` worktree | was 4 commits | **Yes** (merged `d0a19f6`, published) | n/a | none | **Yes** | `git worktree remove` then `git branch -d release/0.4.4` (archive tag optional) |
| `spike/v3d-ajv-validate` (+ worktree) | 3 commits, superseded | via 0.4.2 + 0.4.4 | tag `archive/spike-v3d-ajv-validate` | none | **Yes** | keep archive tag; `git branch -D` |
| `feature/vr-639-640-spec-cli-prereqs` (primary, dirty) | committed superseded; **dirty = META-157 work** | spec parts via 0.4.4 | tag + patch | **META-157 open** | **No** | hold until META-157 lands; never `checkout .`/`reset --hard`/`clean` |
| `fix/npm-*` (4), `fix/agents-audit-bin-entrypoint`, `fix/ci-pnpm-version`, `release/0.4.2`, `release/v0.3`, `codex-root-workspace-json` | none (`git cherry`=0) | Yes | git history | none | **Yes** | optional archive tag; `git branch -d` |
| 8 prunable worktree admin entries (missing dirs) | none | n/a | n/a | none | **Yes** | `git worktree prune -v` |
| `docs/readme-clarity-final` (PR #18) | 1 commit | No | n/a | **PR #18 open** | **No** | resolve PR #18 first |
| `docs/reconciliation-audit-2026-07-22` (PR #20) | 2 commits | on merge | n/a | PR #20 | after merge | delete branch after PR #20 merges |
| `preserve/…` tag, dangling stashes, `v0.4.4` tag | preservation / history | — | — | META-157 (preserve tag) | **No** | retain; not cleanup targets this cycle |

No cleanup command was run; a separate authorization is required to execute it.

## Final Outcome

- Canonical `origin/main`: **`d0a19f6`** (0.4.4 merged).
- Published + verified: `@workspacejson/spec`/`rules`/`agents-audit@0.4.4`.
- META-101, META-102: **Done** (post-verification). META-157: created for the deferred CLI work.
- PR #20: merged (this addendum is its final content).
- Primary dirty checkout, preservation tag, stashes, worktrees, branches: **all intact** — cleanup deferred to a separate authorization.

**Result: `AUTHORIZE_FINAL_CLEANUP`.**
