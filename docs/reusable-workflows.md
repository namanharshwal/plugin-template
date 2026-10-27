# Reusable Workflows Guide

This repository (`ubiquity-os/plugin-template`) provides reusable GitHub Actions workflows (`workflow_call`) for Jest testing and Knip dead code analysis.

By centralizing these workflows, downstream plugins and template-based repositories can inherit continuous integration updates without duplicating workflow definitions.

---

## 1. Jest Testing Workflow (`jest-testing.yml`)

Runs automated unit/integration tests with coverage and generates step summaries.

### Supported Package Managers

- `bun` (default)
- `yarn` (v1, v2+, Berry)
- `npm`
- `pnpm`

### Usage Example

In any repository (e.g. `.github/workflows/jest-testing.yml`):

```yaml
name: Run Jest testing suite

on:
  pull_request:
  workflow_dispatch:

jobs:
  test:
    uses: ubiquity-os/plugin-template/.github/workflows/jest-testing.yml@development
    with:
      package-manager: "bun" # or "yarn" | "npm" | "pnpm"
```

### Inputs Reference

| Input               | Description                                           | Type     | Default               |
| :------------------ | :---------------------------------------------------- | :------- | :-------------------- |
| `package-manager`   | Package manager to use (`bun`, `yarn`, `npm`, `pnpm`) | `string` | `"bun"`               |
| `bun-version`       | Bun version to install when using Bun                 | `string` | `"latest"`            |
| `node-version`      | Node.js version to install when using Yarn/NPM/pnpm   | `string` | `"24.x"`              |
| `test-command`      | Custom test execution command (overrides default)     | `string` | `""`                  |
| `install-command`   | Custom dependency install command                     | `string` | `""`                  |
| `working-directory` | Root directory for execution                          | `string` | `"."`                 |
| `dashboard-file`    | Path to markdown file for step summary                | `string` | `"test-dashboard.md"` |

---

## 2. Knip Workflow (`knip.yml`)

Performs unused dependency, export, and code analysis with automated result packaging.

### Usage Example

In downstream repository (e.g. `.github/workflows/knip.yml`):

```yaml
name: Knip

on:
  pull_request:
  workflow_dispatch:

jobs:
  knip:
    uses: ubiquity-os/plugin-template/.github/workflows/knip.yml@development
    with:
      package-manager: "bun"
```

### Inputs Reference

| Input               | Description                                    | Type     | Default    |
| :------------------ | :--------------------------------------------- | :------- | :--------- |
| `package-manager`   | Package manager (`bun`, `yarn`, `npm`, `pnpm`) | `string` | `"bun"`    |
| `bun-version`       | Bun runtime version                            | `string` | `"latest"` |
| `node-version`      | Node.js runtime version                        | `string` | `"24.x"`   |
| `knip-command`      | Custom knip execution command                  | `string` | `""`       |
| `install-command`   | Custom dependency installation command         | `string` | `""`       |
| `working-directory` | Directory where knip runs                      | `string` | `"."`      |

---

## 3. Knip Reporter Workflow (`knip-reporter.yml`)

Reports Knip findings directly as a comment on the pull request upon failed Knip runs.

### Usage Example

```yaml
name: Knip-reporter

on:
  workflow_run:
    workflows: ["Knip"]
    types:
      - completed

jobs:
  reporter:
    uses: ubiquity-os/plugin-template/.github/workflows/knip-reporter.yml@development
    secrets:
      token: ${{ secrets.GITHUB_TOKEN }}
```
