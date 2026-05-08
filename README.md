# agents-audit

[![npm version](https://img.shields.io/npm/v/agents-audit)](https://www.npmjs.com/package/agents-audit)
[![License: Apache 2.0](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://github.com/workspace-json/agents-audit/blob/main/LICENSE)
[![workspace.json spec](https://img.shields.io/badge/workspace.json-v0.1-green)](https://www.workspacejson.dev/spec/)

**Audit tool for AGENTS.md hygiene and workspace.json consistency.**

`agents-audit` is the reference CLI for the [workspace.json open standard](https://www.workspacejson.dev). Run it in any repository to get a hygiene score, actionable findings, and validation of your AI agent context files.

## Quick Start

```bash
npx agents-audit scan .
```

No installation required. Run from your repository root.

## What It Does

- **Audits `AGENTS.md`** — checks for structure, completeness, and common hygiene issues
- **Validates `agents.workspace.json`** — verifies schema compliance against the workspace.json v0.1 spec
- **Produces a hygiene score** — 0–100 score with error/warning/info level findings
- **CI-ready** — use `--fail-on=error` to block PRs on hygiene errors

## Installation

```bash
# Global install
npm install -g agents-audit

# Or run without installing
npx --yes agents-audit
```

## Usage

```bash
# Basic audit
agents-audit scan .

# Fail CI on errors
agents-audit scan . --fail-on=error

# JSON output
agents-audit scan . --json

# Specify repo path
agents-audit scan /path/to/repo
```

`scan` takes the repository path as a positional argument. The `--dir` flag is not supported.

## What is workspace.json?

[workspace.json](https://www.workspacejson.dev) is the open specification for structured AI agent codebase intelligence. An `agents.workspace.json` file committed to `.agents/agents.workspace.json` in your repository gives AI coding agents (Claude Code, Cursor, Goose, Cline, etc.) structured facts about your codebase without requiring them to read every file.

workspace.json complements AGENTS.md:
- **AGENTS.md** — prescriptive (human-authored, "what should agents do")
- **workspace.json** — descriptive (machine-generated, "what is true about the code")

## GitHub Actions

```yaml
- name: Audit AGENTS.md and workspace.json
  run: npx --yes agents-audit --fail-on=error
```

## Packages in This Repo

This monorepo publishes three packages:

| Package | Description |
|---------|-------------|
| [`agents-audit`](https://www.npmjs.com/package/agents-audit) | The CLI tool |
| [`@workspacejson/spec`](https://www.npmjs.com/package/@workspacejson/spec) | JSON Schema and TypeScript types for `agents.workspace.json` |
| [`@workspacejson/rules`](https://www.npmjs.com/package/@workspacejson/rules) | The deterministic rule engine |

## Documentation

Full documentation at **[workspacejson.dev/audit/](https://www.workspacejson.dev/audit/)**

## License

Apache 2.0 — free to use, modify, and distribute.

Maintained by the team at [vreko.dev](https://vreko.dev). workspace.json is proposed for donation to the [Alliance for Agent Interoperability Framework (AAIF)](https://www.workspacejson.dev/governance/).

## Release Links

- [GitHub Releases](https://github.com/workspace-json/agents-audit/releases)
- [agents-audit on npm](https://www.npmjs.com/package/agents-audit)
- [@workspacejson/rules on npm](https://www.npmjs.com/package/@workspacejson/rules)
- [@workspacejson/spec on npm](https://www.npmjs.com/package/@workspacejson/spec)
