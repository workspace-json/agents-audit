# agents-audit monorepo

<p align="center">
  <a href="https://github.com/workspace-json">
    <picture>
      <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/workspace-json/agents-audit/main/assets/workspace-json-lockup-dark.png">
      <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/workspace-json/agents-audit/main/assets/workspace-json-lockup-light.png">
      <img src="https://raw.githubusercontent.com/workspace-json/agents-audit/main/assets/workspace-json-lockup-light.png" alt="workspace.json — Portable Repository Intelligence" width="760">
    </picture>
  </a>
</p>

**workspace.json** is an open specification for committed repository
intelligence. A portable JSON artifact at `.agents/workspace.json` combines
producer-generated repository metadata with evidence authored by humans or
specialized tools.

([spec](./packages/spec/) · [rendered docs](https://workspacejson.dev/spec/) · [changelog](./CHANGELOG.md))

**Shipped consumer integrations:**
- [Buildomator (formerly gsd-plugin)](https://buildomator.com) — independent Claude Code implementation reading `.agents/workspace.json` at SessionStart
- [`@workspacejson/codex-mcp`](https://www.npmjs.com/package/@workspacejson/codex-mcp) — Codex MCP server for consulting repository intelligence before edits

`workspace.json` separates producer-generated topology from human-authored manual
evidence. Regeneration updates producer-owned sections while preserving manual
evidence verbatim. See the public
[Billfold fixture](https://github.com/workspace-json/billfold) for a reproducible
example of generated repository metadata combined with human-authored behavioral
evidence.

This repository is the canonical source for the agents-audit release family.

## Packages

| Package | Purpose |
| --- | --- |
| [`@workspacejson/spec`](https://www.npmjs.com/package/@workspacejson/spec) | JSON Schema and TypeScript types for `workspace.json` |
| [`@workspacejson/rules`](https://www.npmjs.com/package/@workspacejson/rules) | Deterministic parser, scanner, validator, and rule engine |
| [`agents-audit`](https://www.npmjs.com/package/agents-audit) | CLI for scanning `AGENTS.md` hygiene and workspace metadata |

## Spec Source

The canonical specification lives at [`packages/spec/`](./packages/spec/).

- JSON Schema: [`packages/spec/schema/v1.json`](./packages/spec/schema/v1.json)
- TypeScript types: [`packages/spec/src/types.ts`](./packages/spec/src/types.ts)
- Rendered: [workspacejson.dev/spec/](https://www.workspacejson.dev/spec/)

## Quick Start

```bash
# Audit AGENTS.md hygiene in any repo
npx agents-audit

# Generate .agents/workspace.json
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
