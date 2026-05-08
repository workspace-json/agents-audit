# Changelog

## Unreleased

- Tightened `agents.workspace.json` validation for package paths and `generatedAt`
- Added regression tests for CLI scan paths, fail-on exit codes, report output, and interactive navigation
- Documented the positional `scan <path>` CLI contract and removed the unsupported `--dir` example
- Restructured repository docs and CI so the workspace reads like a maintained package monorepo and self-audit now fails CI
- Moved the canonical workspace snapshot to root-level `agents.workspace.json` and kept a legacy read fallback for `.agents/agents.workspace.json`

## 0.1.1 - 2026-05-06

- Added npm discoverability keywords to `@workspacejson/spec`
- Added npm discoverability keywords to `@workspacejson/rules`
- Added npm discoverability keywords to `agents-audit`

## 0.1.0 - 2026-05-06

- Initial workspace implementation for `agents-audit`
- Added `@workspacejson/spec` and `@workspacejson/rules`
- Added the `agents-audit` CLI wrapper
