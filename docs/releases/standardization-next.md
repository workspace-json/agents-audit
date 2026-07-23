# Standardization release — `workspace-json/standard` + fixed-group publish

**Status:** planning capture (branch `release/standardization-next-0.4.x`, off `main`). **Not for merge/publish until after the judging-review window.** This is a v0.4.x standardization release — **not** the v0.5 reshape.

## Ratified topology decision (supersedes the fork)

- **Canonical repository: `workspace-json/standard`** — rename `agents-audit` → `standard` (HAC-133). Preserves one history, one release train, existing provenance, fixed-group packages.
- **Rejected/deferred:** reviving a standalone `workspace-json/spec` and splitting the monorepo (HAC-194) — re-pins schema provenance, disrupts source links, needs consumer coordination. Close HAC-194 with this decision record.
- **Spelling canon (unchanged):** GitHub org `workspace-json`, npm scope `@workspacejson`, domain `workspacejson.dev`. Name is always lowercase `workspace.json`.

## Version

`agents-audit`, `@workspacejson/spec`, `@workspacejson/rules` are all published at **0.4.4** (verified on the registry, 2026-07-23). The standardization release is therefore **0.4.5** — a coordinated fixed-group patch bump (repository metadata + provenance change together). Confirm against the registry immediately before cutting, in case another publish lands.

## Scope — issues folded into this release

| Scope | Issue |
|---|---|
| Repo rename + branding rollout (repo/site/codex-mcp, BRANDING.md) | HAC-133 |
| Package `repository`/`bugs`/`directory`/homepage/keywords across every `@workspacejson/*` | HAC-166 |
| Fixed-group npm provenance (receipt: version + commit + integrity + schema hash) | HAC-183 |
| verify-published cross-surface distribution gate | HAC-179 |
| Version-constant census (HAC-179's test corpus) | HAC-204 |
| Deferred CLI behavior corrections + red-first tests | META-157 |
| Release-workflow registry-propagation false-red | META-158 |
| Pre-donation history contamination audit (run before rename/announcement) | META-138 |

**Explicitly excluded** (separate strategic increments — do NOT make release blockers): HAC-118 (manual/hints governance), META-136 (`@workspacejson/evidence`), META-137 (commons intelligence), and any v0.5 reshape (`specVersion → version`, representation convergence, removing stable read paths, schema redesign).

## Open decisions (before execution)

1. **`v1.json` naming** (HAC-166): is `schema/v1.json` misnamed for a v0.4 package, or is a real v1 present and the story is wrong elsewhere?
2. **audit CLI/package fate** (HAC-133 R-0.1): publish `@workspacejson/audit` + keep `agents-audit` bin alias one major + `npm deprecate agents-audit` with pointer — Wave B, below.
3. **Judging-review end timestamp** — the gate below needs the official value; not yet recorded.
4. **`workspace.json Standard` Linear project** currently sits under the **Vreko** team / **Vreko Platform** initiative — conflicts with the clean-room/AAIF "stands independently" posture. Decide whether to reparent to a neutral owner.

## Two publication waves

**Wave A — canonical repository (this release train):** rename repo → `standard`; keep scope `@workspacejson`; publish the three existing packages at 0.4.5 with corrected metadata + provenance; verify registry receipts, package bytes, schema hash, URLs, commands.

**Wave B — CLI identity (separate, later):** publish `@workspacejson/audit`; keep `agents-audit` bin as a compatibility bridge; `npm deprecate agents-audit` with pointer; announce migration. Kept separate so an npm identity migration does not block the canonical-repository correction.

## Coordinated branches (one train, not one literal branch)

```
workspace-json/agents-audit(→standard)  release/standardization-next-0.4.x   ← this branch
workspace-json/website                  chore/standard-repository-rollout
workspace-json/codex-mcp                chore/standard-repository-links
```

Keep commits separable for rollback boundaries: (1) CLI behavior corrections · (2) package metadata · (3) branding/self-references · (4) release workflow + provenance · (5) version bump + changelog.

## Judging gate (freeze)

Supersedes the expired HAC-180 (which froze only through Jul 21). Until the official judging-review end timestamp **[TODO: record exact value]**: stage changes on private/local branches; do **not** rename repositories, alter judge-visible URLs, publish packages, or merge public-facing branding.

## Execution order (after judging)

1. META-138 history audit → 2. confirm registry versions + clean `main` → 3. close HAC-194 (ruling) → 4. land META-157 + red-first tests → 5. HAC-166 metadata across all packages → 6. add `BRANDING.md` + naming canon → 7. provenance receipts + verify-published gates (HAC-179/183/204) → 8. rename repo → `standard` → 9. merge monorepo, then website, then codex-mcp link PRs → 10. publish all three fixed-group packages from one governed commit → 11. verify registry versions, integrity, schema hash, URLs, commands → 12. publish migration note → 13. begin Wave B (`@workspacejson/audit`).

_Linear umbrella tracking this: see the "Next v0.4.x — standardize workspace-json/standard" issue under the workspace.json Development project._
