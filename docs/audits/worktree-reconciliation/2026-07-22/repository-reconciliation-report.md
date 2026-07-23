# Repository & Linear Reconciliation Report — `agents-audit`

**Audit date:** 2026-07-22 · **Mode:** read-only evidentiary audit · **State modified:** none (no commits, pushes, merges, branch/worktree/stash deletions, or Linear writes)

> Governance note: this pass honors META-38 ("surface impl/spec divergence to the human; do NOT auto-resolve"). Where implementation and Linear disagree, the divergence is reported and a proposed action is drafted — nothing is resolved.

---

## Executive Finding

- **Worktrees:** 12 (`git worktree list`) — 3 live-on-disk + clean, 8 with a missing directory (prunable admin entries), 1 primary checkout that is **dirty**.
- **Branches with unique unmerged work:** 4 — `feature/vr-639-640-spec-cli-prereqs`, `release/0.4.4`, `spike/v3d-ajv-validate`, `docs/readme-clarity-final`. The other 10 local branches are patch-identical to `origin/main` (`git cherry` = 0).
- **Uncommitted work surfaces:** 1, and it is **the highest-risk item in the repo** — see below.
- **Relevant Linear issues:** META-101, META-102 (both Urgent/Done), META-97 (census anchor), META-31 (release pipeline), plus the orphan `@workspacejson/cli` shim.
- **Implementation/Linear discrepancies:** **2 material** — META-101 and META-102 are marked **Done** but their fixes are **absent from `origin/main` (the published 0.4.3 line)**.
- **Highest-risk potential loss:** the **uncommitted working tree** on the primary checkout (`feature/vr-639-640-spec-cli-prereqs`). It is the *only copy* of the 0.4.4 split-brain CHANGELOG narrative and 2 unique CLI integration tests. A `git checkout .`/`git stash drop`/`git reset --hard` destroys them.
- **Most important integration dependency:** `release/0.4.4` is the clean vehicle that carries the META-101/102 fixes + `@workspacejson/cli` + the real AJV validator, rebased on current `main` — but it has **no PR** and is **missing** the primary checkout's 2 unique tests + narrative. Those must be ported before the feature branch is torn down.
- **Is the repo safe to clean up now?** **No.** Cleanup must wait until (a) the uncommitted tree is preserved, (b) `release/0.4.4` is merged, and (c) META-101/102 are reconciled.

---

## Repository Baseline

| Item | Value | Basis |
|---|---|---|
| Remote (`origin`) | `https://github.com/workspace-json/agents-audit` | OBSERVED `git remote -v` |
| Canonical branch | **`main`** (`origin/HEAD → origin/main`) | OBSERVED `git remote show origin` |
| Canonical HEAD | `5a0e37e` (Merge PR #17) | OBSERVED |
| Canonical versions | spec `0.4.3`, agents-audit `0.4.3` | OBSERVED `git show origin/main:*/package.json` |
| Published npm | `0.4.3` | INFERRED (memory HAC-179 + origin/main; `npm view` blocked by local publish-fence hook) |
| PR-required workflow | Yes — all history landed via PR #1–#17 | OBSERVED merge commits |
| Protected branch | `main` | INFERRED (PR-only history) |
| Validation gates | `pnpm install`, `pnpm build`, `pnpm typecheck`, `pnpm test` | OBSERVED CLAUDE.md + `.github/workflows/ci.yml` |
| Linear | workspace `marcelle-labs`, team **Meta / Agent Infrastructure** | OBSERVED via Linear MCP |
| Working tree | **dirty** — 15 tracked modified, untracked `packages/spec/src/validator.ts` + `claudedocs/` | OBSERVED `git status` |

> ID caveat (INFERRED): the repo branch prefix `vr-639-640` does **not** match Linear's `gitBranchName`. `VR-639`/`VR-640` resolve by content to **META-101 / META-102** (different git-branch slugs). Treat the `vr-*` names as informal.

---

## Worktree and Branch Inventory

| Surface | Path | Branch / Commit | Unique Work | Linear | Validation | Risk | Proposed Disposition |
|---|---|---|---|---|---|---|---|
| WT-primary | `<repo-root>` | `feature/vr-639-640-spec-cli-prereqs` @ `b6c092b` **(dirty)** | Committed: superseded. **Uncommitted: unique** | META-101/102 | not run (read-only) | **HIGH** | `MANUAL_RECONCILIATION_REQUIRED` |
| WT-release-044 | `/private/tmp/agents-audit-release-044` | `release/0.4.4` @ `3a77e1b` (clean) | Yes — full 0.4.4 candidate | META-101/102 | CI n/a | MEDIUM | `MERGE_CANDIDATE` (open PR) |
| WT-v3d-ajv | `/private/tmp/agents-audit-v3d-ajv` *(dir missing)* | `spike/v3d-ajv-validate` @ `aee5f97` | Superseded by 0.4.2 + 0.4.4 | — | — | LOW | `SUPERSEDED` |
| WT-readme | `/private/tmp/agents-audit-readme-final` | `docs/readme-clarity-final` @ `a91ec1d` (clean) | 1 doc commit | — | PR #18 MERGEABLE | LOW | `MERGE_CANDIDATE` |
| WT-bin | `/private/tmp/agents-audit-bin-entrypoint` | `fix/agents-audit-bin-entrypoint` @ `161cffb` (clean) | None (PR #15 merged) | — | — | LOW | `ALREADY_INTEGRATED` |
| WT-release-042 | *(dir missing)* | `release/0.4.2` @ `316e00f` | None (PR #10 merged) | — | — | LOW | `ALREADY_INTEGRATED` |
| WT-npm-access | *(dir missing)* | `fix/npm-access-command` @ `e3e076d` | None (PR #13) | — | — | LOW | `ALREADY_INTEGRATED` |
| WT-npm-preflight | *(dir missing)* | `fix/npm-publish-access` @ `c3e69f3` | None (PR #11) | — | — | LOW | `ALREADY_INTEGRATED` |
| WT-npm-publisher | *(dir missing)* | `fix/npm-publisher-preflight` @ `cf8ff0e` | None (PR #14) | — | — | LOW | `ALREADY_INTEGRATED` |
| WT-npm-token | *(dir missing)* | `fix/npm-token-routing` @ `9f5af72` | None (PR #12) | — | — | LOW | `ALREADY_INTEGRATED` |
| WT-v3d-baseline | *(dir missing)* | detached @ `4ab426e` | None (reachable) | — | — | LOW | `ABANDON_CANDIDATE` |
| WT-hac-181 | *(dir missing)* | detached @ `8acc068` | None (= main base) | — | — | LOW | `ABANDON_CANDIDATE` |

**Non-worktree local branches:** `codex-root-workspace-json` (cherry=0 → `ALREADY_INTEGRATED`), `fix/ci-pnpm-version` (PR #2), `release/v0.3` (PR #1), local `main` (stale, behind 21 → `REBASE_OR_REFRESH_CANDIDATE`, no unique work). **Remote-only:** `origin/fix/verify-published-registry-propagation-retry` (PR #16, integrated).

**Stashes:** `git stash list` is **empty**. `git fsck --unreachable` surfaces 15 dangling stash-shaped commits — 4 recent ones anchored on the feature branch (2026-07-16→19) that may hold earlier snapshots of the current uncommitted work (`PRESERVE_PENDING_DECISION`), and 11 old May-2026 snapshots on already-merged history (`ABANDON_CANDIDATE`).

---

## Linear Reconciliation Matrix

| Linear Issue | Current Linear State | Observed Implementation State | Evidence | Proposed Update |
|---|---|---|---|---|
| **META-101** (VR-639) | **Done** / Urgent | `IMPLEMENTED_UNVERIFIED` — **not integrated** | `files: string[]` set-semantics present on feature/spike/release-0.4.4; `origin/main:types.ts:94` still `files: [string, string]`. | Comment: fix on branches only, not on published 0.4.3; ships with 0.4.4. **Hold status for human** (META-31). |
| **META-102** (VR-640) | **Done** / Urgent | `IMPLEMENTED_UNVERIFIED` — **not integrated** | Anchor "repository-root-relative POSIX" appears 2× in schema on feature/spike/release-0.4.4; **0×** on `origin/main`. | Same as META-101. |
| **META-97** | Backlog / High | `PARTIAL` | This audit is the census META-97 anchors (this pass also proposes dispositions, exceeding its inventory-only scope). | Comment linking this report; human decides fulfillment. |
| **META-31** | Backlog / High | `REFERENCE` | Root cause of "Done ≠ released" ambiguity behind META-101/102. | No change; cited as context. |
| `@workspacejson/cli` (no clear issue) | — | `ORPHAN_IMPLEMENTATION` | `packages/cli` on feature/spike/release-0.4.4; absent on `origin/main`. References vreko#491 / HAC-75 as CLI-scaffold home. | Confirm/create owning issue for the shim publish. |

**Headline discrepancy:** two Urgent issues were closed **Done** on 2026-07-12 describing spec fixes "on branch `feature/vr-639-640-spec-cli-prereqs`" — but that branch was **never merged**, and `main` advanced to 0.4.3 through a *different* release line (0.4.2 → 0.4.3) that did **not** carry those fixes. The published spec still ships the exact tuple contradiction (META-101) and unanchored fileIndex (META-102) both issues declared fixed. This is a "Done-but-unshipped" gap, precisely the class META-31 exists to close.

---

## Duplicate and Superseded Work

Verified with `git patch-id --stable`:

- **`scaffold @workspacejson/cli`** — **identical patch** `e1b4588` on `feature` (`4ab426e`), `spike` (`4ab426e`), and `release/0.4.4` (`8dca4e7`). Not on `main`. → one change, three carriers; `DUPLICATE_PATCH`.
- **`make workspace generation producer-conformant`** — `b6c092b` (feature, `725deb2`) **diverges** from `0be09a1` (merged via 0.4.2, `7fc421a`). Same message, different content → `IMPLEMENTATION_DIVERGED`. The 0.4.2 flavor is already in `main`.
- **`pin fileIndex key format`** — `aca3192` (feature/spike, `95075cb`) **diverges** from `0566ee9` (release/0.4.4, `751cdaa`).
- **`validate against packaged schema`** — spike `aee5f97` (`19bc1b2`) **superseded** by productionized `6c1c01a` ("ship strict packaged validator and CLI") merged to `main` via 0.4.2. The untracked `validator.ts` in the primary checkout is **byte-identical** to `origin/main`'s tracked copy.

**Net:** `spike/v3d-ajv-validate` is fully superseded. `feature/vr-639-640`'s *committed* content is superseded by `release/0.4.4`; only its *uncommitted* delta is unique.

---

## Conflict and Dependency Graph

```
origin/main (5a0e37e, 0.4.3)  ──ships──►  META-101/102 fixes ABSENT  ⚠ discrepancy
      │
      ├── release/0.4.4 (behind 2)  ── refresh ──►  carries META-101/102 + @workspacejson/cli + real validator
      │        ▲
      │        │ MUST PORT (else lost): 2 unique CLI tests + 0.4.4 CHANGELOG narrative
      │        │
      │   feature/vr-639-640 (dirty, stale 0.4.1 base)  ── only copy of uncommitted delta
      │
      ├── spike/v3d-ajv-validate  ── SUPERSEDED (validator already in main via 0.4.2)
      │
      └── docs/readme-clarity-final  ── PR #18 (independent, parallelizable)
```

- **Must land first:** `release/0.4.4` (foundational — publishes the spec fixes + CLI).
- **Blocked-on-preservation:** feature branch teardown (holds only-copy content) and spike teardown (until 0.4.4 lands).
- **Parallel-safe:** docs PR #18; worktree-prune of the 8 missing-dir entries.
- **Do not merge:** `spike` and the raw `feature` committed history (both diverge/duplicate) — port selectively instead.
- **No secrets/credentials** were found in scope; the `fix/npm-*` branches touch CI token *routing config*, not secret values (all already merged).

---

## Proposed Merge Sequence

- **Wave 0 — Preservation & prerequisites** *(parallel)*: snapshot the primary checkout's uncommitted tree to a preservation branch + patch bundle + tag; archive-tag `spike/v3d-ajv-validate`; confirm `release/0.4.4` is clean and pushed.
- **Wave 1 — Foundational**: refresh `release/0.4.4` from `origin/main` (behind 2); **port** the 2 unique CLI tests + the 0.4.4 CHANGELOG split-brain narrative from the primary checkout into `release/0.4.4`; open PR → `main`; pass CI; merge.
- **Wave 2 — Dependent**: merge docs **PR #18**; publish **0.4.4** via the Release workflow (fixed-group bump — note the version inconsistency in G4).
- **Wave 3 — Validation & Linear**: verify published 0.4.4 carries META-101/102 fixes + `@workspacejson/cli`; update META-101/102 to reflect *released*; confirm/close the CLI shim issue; comment META-97 with this report.
- **Wave 4 — Cleanup**: `git worktree prune`; delete integrated local branches after archive tags; retire `spike` + `feature/vr-639-640` after preservation; prune old May dangling stashes.

*Parallelizable:* Wave 0 tasks; docs PR #18 (any time); worktree-prune (any time — dirs already gone).

---

## Proposed Linear Updates (paste-ready — NOT applied)

**META-101 — comment (hold status for human):**
> Reconciliation audit 2026-07-22: the `coChange.files` set-semantics fix is present on `feature/vr-639-640-spec-cli-prereqs`, `spike/v3d-ajv-validate`, and `release/0.4.4`, but is **not** on `origin/main` — 0.4.3 still ships `files: [string, string]` in `types.ts:94`. This issue was closed Done on 2026-07-12 but never integrated to the canonical/published branch. It ships with `release/0.4.4` (currently unmerged, no PR). Recommend keeping Done only if "Done" means merged-to-branch; otherwise reopen until 0.4.4 publishes (see META-31).

**META-102 — comment (hold status for human):**
> Reconciliation audit 2026-07-22: the `fileIndex` repo-root-relative POSIX anchor is present on `feature`/`spike`/`release/0.4.4` but absent from `origin/main` (0 matches for "repository-root-relative" in `schema/v1.json` at 0.4.3). Done 2026-07-12 but not integrated/published. Ships with `release/0.4.4`.

**META-97 — comment (no status change):**
> Branch/worktree census delivered at `docs/audits/worktree-reconciliation/2026-07-22/`. 12 worktrees (3 live, 8 prunable, 1 primary-dirty); 14 local branches, 4 with unique unmerged work (`feature/vr-639-640`, `release/0.4.4`, `spike/v3d-ajv-validate`, `docs/readme-clarity-final`). Zero repo/Linear mutations in this pass.

---

## Cleanup Candidates (deferred — nothing deleted)

| Target | Safe only after | Later command |
|---|---|---|
| 8 prunable worktree admin entries | now (dirs already gone) | `git worktree prune -v` |
| `fix/npm-*`, `fix/agents-audit-bin-entrypoint`, `fix/ci-pnpm-version`, `release/0.4.2`, `release/v0.3`, `codex-root-workspace-json` | archive tag/bundle | `git branch -d <b>` |
| `spike/v3d-ajv-validate` | 0.4.4 merged + archive tag | `git branch -D spike/v3d-ajv-validate` |
| `feature/vr-639-640-spec-cli-prereqs` | **unique uncommitted content ported** + snapshot | teardown only after G2 |
| 11 old May dangling stashes | confirm superseded | `git gc` (implicit) |

**No branch or worktree is proposed for deletion on age or name alone.** Each carries an explicit "safe only after" precondition.

---

## Items Requiring Human Judgment

1. **G1** — Ratify `release/0.4.4` (not `feature/vr-639-640`) as the blessed 0.4.4 vehicle.
2. **G2** — Confirm the 2 unique CLI tests + CHANGELOG narrative must be ported before feature-branch teardown.
3. **G3** — Decide META-101/102 status semantics (keep Done vs reopen-until-released).
4. **G4** — Confirm the fixed-group version bump: the feature working tree bumps **only** spec→0.4.4 (leaves agents-audit at 0.4.1), whereas `release/0.4.4` bumps **all** packages→0.4.4. These disagree.
5. **G5** — Authorize any branch/worktree deletion (none performed here).

---

## Unknowns and Missing Evidence

- Live npm versions — `npm view` blocked by the local architecture-fence hook; 0.4.3 is INFERRED.
- Byte-equality of local `release/0.4.4` vs `origin/release/0.4.4` (tracking shown, not diffed).
- Whether a dedicated Linear issue tracks the split-brain incident / 0.4.4 publish (memory calls it "HAC-199"; not located this pass — META-101's honest-correction section may be the only record).
- The owning issue for the `@workspacejson/cli` shim publish (references vreko#491 / HAC-75).
- Contents of the 4 recent feature-branch dangling stashes vs the current working tree (not diffed).

---

## Final Recommendation

**Proceed with remediation — but Wave 0 preservation first, and only under a separate execution authorization.**

The single most important action is **non-destructive preservation of the primary checkout's uncommitted working tree** (the split-brain CHANGELOG narrative + 2 CLI tests) before anything else touches this repo — it is the only copy and the only genuinely at-risk work. Everything else (integrating `release/0.4.4`, reconciling META-101/102, cleaning prunable worktrees) is recoverable from committed history and can follow in sequence.

**Safest first action:** on the primary checkout, create a preservation snapshot of the working tree (e.g. a dedicated preservation branch commit + `git bundle`/patch), *without* merging, pushing, or discarding — so the reconciliation delta survives independent of any later branch decision.
