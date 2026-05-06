# agents-audit monorepo

This repository is the canonical source for the `agents-audit` release family.

Published packages:

- [`@workspacejson/spec`](https://www.npmjs.com/package/@workspacejson/spec)
- [`@workspacejson/rules`](https://www.npmjs.com/package/@workspacejson/rules)
- [`agents-audit`](https://www.npmjs.com/package/agents-audit)

What lives here:

- The JSON Schema and types for `agents.workspace.json`
- The deterministic rule engine and fixtures
- The `agents-audit` CLI and presentation layer
- Release metadata, CI, and packaging configuration

All three npm packages are published from this single monorepo and point back to
`workspace-json/agents-audit` in their package metadata.

Homepage: https://workspacejson.dev

Releases are intended to use GitHub Actions trusted publishing with provenance,
not long-lived npm tokens.

GitHub release tags mirror npm package versions so the repository history and the
registry history stay aligned.
