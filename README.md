# agents-audit monorepo

**workspace.json** is an open specification for structured AI agent codebase
intelligence. A machine-generated JSON file at `.agents/agents.workspace.json`
gives AI coding agents structured context about your repository.

**Current version: v0.3** ([spec](./packages/spec/) · [rendered docs](https://workspacejson.dev/spec/) · [changelog](./CHANGELOG.md))

**Shipped consumer integrations:**
- [`jnuyens/gsd-plugin v2.42.3`](https://github.com/jnuyens/gsd-plugin/releases/tag/v2.42.3) — Claude Code plugin reading `.agents/agents.workspace.json` at SessionStart

This repository is the canonical source for the agents-audit release family.

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
