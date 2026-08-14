<!-- markdownlint-disable -->

# Hardening Report: MishaKav--pytest-coverage-comment/v1.8.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **MishaKav--pytest-coverage-comment/v1.8.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Multiple run: blocks directly interpolate ${{ steps.coverageComment.outputs.* }} expressions into shell commands. The steps.*.outputs.* context is workflow-controllable and flows through YAML template substitution before the shell sees it, allowing an attacker who controls the action's outputs to inject arbitrary shell commands. Affected steps and offending lines:

live-test.yml — 'Check the output coverage' step: echo "Coverage Percentage - ${{ steps.coverageComment.outputs.coverage }}", echo "Coverage Html - ${{ steps.coverageComment.outputs.coverageHtml }}", echo "Not Success Test Info - ${{ steps.coverageComment.outputs.notSuccessTestInfo }}", etc.

test-branch.yml — 'Check the output coverage' step: echo "Coverage Report - ${{ steps.coverageComment.outputs.coverage }}", etc.

update-coverage-in-readme.yml — 'Check the output coverage' step: echo "Coverage Percentage - ${{ steps.coverageComment.outputs.coverage }}", etc.

update-coverage-on-readme.yml — 'Update Readme with Coverage Html' step: sed -i '.../${{ steps.coverageComment.outputs.content }}/...' ./README.md — particularly dangerous as the output is interpolated into a sed command.

Locations:

- `.github/workflows/live-test.yml:23`
- `.github/workflows/test-branch.yml:23`
- `.github/workflows/update-coverage-in-readme.yml:43`
- `.github/workflows/update-coverage-on-readme.yml:33`

### unpinned-uses (severity: high)

Multiple workflow files reference actions using mutable tags or branch names instead of pinned 40-character commit SHAs, making them vulnerable to supply-chain attacks if the referenced tag or branch is moved or compromised. Failing references:

live-test.yml: actions/checkout@v6, MishaKav/pytest-coverage-comment@main, schneegans/dynamic-badges-action@v1.8.0

multiple-files.yml: actions/checkout@v6, MishaKav/pytest-coverage-comment@main (×4)

test-branch.yml: actions/checkout@v6, MishaKav/pytest-coverage-comment@main

update-coverage-in-readme.yml: actions/checkout@v6, actions/cache@v5, MishaKav/jest-coverage-comment@main, schneegans/dynamic-badges-action@v1.8.0

update-coverage-on-readme.yml: actions/checkout@v6, MishaKav/pytest-coverage-comment@main, actions-js/push@master

update-main-version.yml: actions/checkout@v6

Locations:

- `.github/workflows/live-test.yml:13`
- `.github/workflows/multiple-files.yml:12`
- `.github/workflows/test-branch.yml:14`
- `.github/workflows/update-coverage-in-readme.yml:19`
- `.github/workflows/update-coverage-on-readme.yml:14`
- `.github/workflows/update-main-version.yml:19`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed script-injection in 4 workflow files (live-test.yml, test-branch.yml, update-coverage-in-readme.yml, update-coverage-on-readme.yml) by moving all ${{ steps.coverageComment.outputs.* }} expressions from run: blocks into env: blocks and referencing them as plain shell variables. The particularly dangerous sed command in update-coverage-on-readme.yml now uses $COVERAGE_CONTENT env var. Fixed unpinned-uses in all 6 workflow files by pinning: actions/checkout@v6→@d23441a48e516b6c34aea4fa41551a30e30af803, MishaKav/pytest-coverage-comment@main→@fd9adbdca9fdfbe2b9bfa44a7340e6a83346ce6c, schneegans/dynamic-badges-action@v1.8.0→@0e50b8bad39e7e1afd3e4e9c2b7dd145fad07501, MishaKav/jest-coverage-comment@main→@58072a21b8b7f84d36a6e21b5d9a0cad08bc9d75, actions-js/push@master→@5a7cbd780d82c0c937b5977586e641b2fd94acc5, actions/cache@v5→@caa296126883cff596d87d8935842f9db880ef25.

