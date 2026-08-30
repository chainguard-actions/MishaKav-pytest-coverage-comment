<!-- markdownlint-disable -->

# Hardening Report: MishaKav--pytest-coverage-comment/v1.12.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **MishaKav--pytest-coverage-comment/v1.12.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Multiple `run:` blocks directly interpolate `${{ steps.coverageComment.outputs.* }}` expressions inside shell commands. `steps.*.outputs.*` is a workflow-controllable context — if the action producing those outputs can be influenced by an attacker (e.g. via crafted coverage files), the interpolated value is executed by the shell before quoting can occur. Affected steps and offending lines:
- live-test.yml "Check the output coverage": `echo "Coverage Percentage - ${{ steps.coverageComment.outputs.coverage }}"` and 9 similar echo lines.
- test-branch.yml "Check the output coverage": `echo "Coverage Report - ${{ steps.coverageComment.outputs.coverage }}"` and 2 similar lines.
- update-coverage-in-readme.yml "Check the output coverage": `echo "Coverage Percentage - ${{ steps.coverageComment.outputs.coverage }}"` and 14 similar echo lines.
- update-coverage-on-readme.yml "Update Readme with Coverage Html": `sed -i '.../${{ steps.coverageComment.outputs.content }}...'` — the output is interpolated directly into a sed command string.

Locations:

- `.github/workflows/live-test.yml:22`
- `.github/workflows/test-branch.yml:22`
- `.github/workflows/update-coverage-in-readme.yml:44`
- `.github/workflows/update-coverage-on-readme.yml:30`

### unpinned-uses (severity: high)

All workflow files reference external actions using mutable tags, branch names, or version strings instead of pinned 40-character SHA commit hashes. This exposes the workflow to supply-chain attacks where a compromised or force-pushed tag/branch could execute arbitrary code. Failing references:
- live-test.yml: `actions/checkout@v7`, `MishaKav/pytest-coverage-comment@main`, `schneegans/dynamic-badges-action@v1.9.0`
- multiple-files.yml: `actions/checkout@v7`, `MishaKav/pytest-coverage-comment@main` (×4)
- test-branch.yml: `actions/checkout@v7`, `MishaKav/pytest-coverage-comment@main`
- update-coverage-in-readme.yml: `actions/checkout@v7`, `actions/cache@v6`, `MishaKav/jest-coverage-comment@main`, `schneegans/dynamic-badges-action@v1.9.0`
- update-coverage-on-readme.yml: `actions/checkout@v7`, `MishaKav/pytest-coverage-comment@main`, `actions-js/push@master`
- update-main-version.yml: `actions/checkout@v7`

Locations:

- `.github/workflows/live-test.yml:13`
- `.github/workflows/multiple-files.yml:12`
- `.github/workflows/test-branch.yml:13`
- `.github/workflows/update-coverage-in-readme.yml:18`
- `.github/workflows/update-coverage-on-readme.yml:14`
- `.github/workflows/update-main-version.yml:20`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all 6 workflow files:

**unpinned-uses**: Pinned all action references to full 40-char SHAs:
- actions/checkout@v7 → @3d3c42e5aac5ba805825da76410c181273ba90b1
- MishaKav/pytest-coverage-comment@main → @f79b34ab549a0f6b59e2d44ddea0d20858026eff
- schneegans/dynamic-badges-action@v1.9.0 → @28b0fa8bdeb46170ac397105ece0c1fe58f68910
- actions/cache@v6 → @55cc8345863c7cc4c66a329aec7e433d2d1c52a9
- MishaKav/jest-coverage-comment@main → @642ef024cc554a34b7082cea12c7bf63575ae151
- actions-js/push@master → @968f4695ca558093eadb24ad83cc5891f47e0cdc

**script-injection**: Moved all ${{ steps.coverageComment.outputs.* }} expressions from run: shell commands into env: blocks in live-test.yml (10 vars), test-branch.yml (3 vars), update-coverage-in-readme.yml (13 vars), and update-coverage-on-readme.yml (sed command now uses $COVERAGE_CONTENT env var). Shell scripts now reference plain environment variables instead of directly interpolating workflow expressions.

