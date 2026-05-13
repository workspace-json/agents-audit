# workspace.json — agents-audit monorepo

**workspace.json** (`agents.workspace.json`) is an open metadata format that gives AI coding assistants structured intelligence about a codebase — fragility scores, framework detection, co-change patterns, and hygiene signals — so they can make better decisions without reading every file.

**Current release: v0.3.0** · [Spec →](https://www.workspacejson.dev/spec/) · [npm →](https://www.npmjs.com/package/agents-audit) · [Changelog →](./CHANGELOG.md)

This monorepo is the canonical home for the workspace.json specification, tooling, and rule engine.

## Packages

| Package | Version | Purpose |
| --- | --- | --- |
| [`@workspacejson/spec`](https://www.npmjs.com/package/@workspacejson/spec) | 0.3.0 | JSON Schema and TypeScript types for `agents.workspace.json` |
| [`@workspacejson/rules`](https://www.npmjs.com/package/@workspacejson/rules) | 0.3.0 | Deterministic parser, scanner, validator, and rule engine |
| [`agents-audit`](https://www.npmjs.com/package/agents-audit) | 0.3.0 | CLI for scanning `AGENTS.md` hygiene and workspace metadata |

## Spec Source

The canonical v0.3 specification lives at [`packages/spec/`](./packages/spec/).

- JSON Schema: [`packages/spec/schema/v1.json`](./packages/spec/schema/v1.json)
- TypeScript types: [`packages/spec/src/types.ts`](./packages/spec/src/types.ts)
- Rendered: [workspacejson.dev/spec/](https://www.workspacejson.dev/spec/)

## Shipped Consumer Integrations

| Tool | Version | Role |
| --- | --- | --- |
| [gsd-plugin](https://github.com/jnuyens/gsd-plugin) | v2.42.3 | First shipped consumer — reads `generated.frameworkManifest`, `generated.fileIndex`, `manual.fragileFiles`, `manual.coChangePatterns` from `.agents/agents.workspace.json` at session start |

## Quick Start

```bash
# Audit AGENTS.md hygiene in any repo
npx agents-audit

# Generate agents.workspace.json
npx agents-audit generate
```

## Repository Layout

```text
agents-audit/
├── packages/
│   ├── spec/          — JSON Schema + TypeScript types
│   ├── rules/         — Rule engine and validator
│   └── agents-audit/  — CLI binary
├── .github/
├── pnpm-workspace.yaml
├── package.json
├── README.md
└── CHANGELOG.md
```

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

## Release Notes

Version history is tracked in [`CHANGELOG.md`](./CHANGELOG.md). GitHub release tags mirror npm package versions.

## Support Files

- [`CONTRIBUTING.md`](./CONTRIBUTING.md)
- [`SECURITY.md`](./SECURITY.md)
- [`AGENTS.md`](./AGENTS.md)
