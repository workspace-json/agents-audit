# agents-audit workspace

Monorepo for the `agents-audit` CLI, the `@workspacejson/spec` schema package, and the `@workspacejson/rules` engine package.

This repository is the canonical workspace for the project. Keep package entrypoints aligned with:

- `packages/spec/src/index.ts`
- `packages/rules/src/index.ts`
- `packages/agents-audit/src/index.ts`
- `packages/agents-audit/src/cli.ts`

## Packages

| Package | Purpose |
| --- | --- |
| `@workspacejson/spec` | JSON Schema and TypeScript types for `agents.workspace.json` |
| `@workspacejson/rules` | Deterministic parser, scanner, validator, and rule engine |
| `agents-audit` | CLI for scanning `AGENTS.md` hygiene and workspace metadata |

## Repository Layout

```text
agents-audit/
├── packages/
│   ├── spec/
│   ├── rules/
│   └── agents-audit/
├── .github/
├── pnpm-workspace.yaml
├── package.json
├── README.md
└── CHANGELOG.md
```

## What Ships

- `packages/spec` publishes the schema and types for `agents.workspace.json`
- `packages/rules` publishes the rule engine and supporting APIs
- `packages/agents-audit` publishes the CLI binary

The CLI reads repository content, validates `AGENTS.md`, and can generate `agents.workspace.json` for a repo snapshot.

## Local Development

```bash
pnpm install
pnpm -r typecheck
pnpm -r test
pnpm -r build
```

To run the CLI against the current repository:

```bash
pnpm --filter agents-audit build
node packages/agents-audit/dist/cli.js scan .
```

## Validation

The repository is expected to validate itself.

- Package exports must resolve
- Package tarballs must pack cleanly
- `agents-audit` must pass on this repository

## Release Notes

Version history is tracked in [`CHANGELOG.md`](./CHANGELOG.md).

## Support Files

- [`CONTRIBUTING.md`](./CONTRIBUTING.md)
- [`SECURITY.md`](./SECURITY.md)
- [`AGENTS.md`](./AGENTS.md)
