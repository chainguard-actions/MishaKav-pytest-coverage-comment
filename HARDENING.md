<!-- markdownlint-disable -->

# Hardening Report: MishaKav--pytest-coverage-comment/v1.10.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **MishaKav--pytest-coverage-comment/v1.10.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Multiple `run:` blocks directly interpolate `${{ steps.coverageComment.outputs.* }}` expressions inside shell commands. These are `steps.*.outputs.*` context values — workflow-controllable data that flows through YAML template substitution before the shell sees it. If the action producing those outputs can be influenced by attacker-controlled input (e.g. coverage file content), this enables script injection. Offending lines include: `echo "Coverage Percentage - ${{ steps.coverageComment.outputs.coverage }}"` and similar in live-test.yml (line 26), test-branch.yml (line 29), update-coverage-in-readme.yml (line 50), and `sed -i '...${{ steps.coverageComment.outputs.content }}...' ./README.md` in update-coverage-on-readme.yml (line 31).

Locations:

- `.github/workflows/live-test.yml:26`
- `.github/workflows/test-branch.yml:29`
- `.github/workflows/update-coverage-in-readme.yml:50`
- `.github/workflows/update-coverage-on-readme.yml:31`

### unpinned-uses (severity: high)

All `uses:` references across every workflow file use mutable tags or branch names instead of full 40-character commit SHAs, making the workflows vulnerable to supply-chain attacks if any referenced action is compromised or its tag is moved. Failing references include: `actions/checkout@v7`, `MishaKav/pytest-coverage-comment@main`, `schneegans/dynamic-badges-action@v1.8.0`, `actions/cache@v6`, `MishaKav/jest-coverage-comment@main`, `actions-js/push@master`.

Locations:

- `.github/workflows/live-test.yml:15`
- `.github/workflows/live-test.yml:19`
- `.github/workflows/live-test.yml:38`
- `.github/workflows/multiple-files.yml:14`
- `.github/workflows/multiple-files.yml:18`
- `.github/workflows/multiple-files.yml:24`
- `.github/workflows/multiple-files.yml:30`
- `.github/workflows/multiple-files.yml:36`
- `.github/workflows/test-branch.yml:16`
- `.github/workflows/test-branch.yml:20`
- `.github/workflows/update-coverage-in-readme.yml:18`
- `.github/workflows/update-coverage-in-readme.yml:22`
- `.github/workflows/update-coverage-in-readme.yml:40`
- `.github/workflows/update-coverage-in-readme.yml:68`
- `.github/workflows/update-coverage-on-readme.yml:14`
- `.github/workflows/update-coverage-on-readme.yml:22`
- `.github/workflows/update-coverage-on-readme.yml:36`
- `.github/workflows/update-main-version.yml:20`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all 5 workflow files:

1. **unpinned-uses** (18 locations across 5 files): Pinned all action references to full 40-char SHAs:
   - actions/checkout@v7 → @9c091bb21b7c1c1d1991bb908d89e4e9dddfe3e0
   - MishaKav/pytest-coverage-comment@main → @fd9adbdca9fdfbe2b9bfa44a7340e6a83346ce6c
   - schneegans/dynamic-badges-action@v1.8.0 → @0e50b8bad39e7e1afd3e4e9c2b7dd145fad07501
   - actions/cache@v6 → @55cc8345863c7cc4c66a329aec7e433d2d1c52a9
   - MishaKav/jest-coverage-comment@main → @58072a21b8b7f84d36a6e21b5d9a0cad08bc9d75
   - actions-js/push@master → @5a7cbd780d82c0c937b5977586e641b2fd94acc5

2. **script-injection** (4 locations): Moved all ${{ steps.coverageComment.outputs.* }} expressions from run: shell strings into env: blocks and referenced them as plain env vars. For update-coverage-on-readme.yml, replaced the dangerous sed command (which interpolated output content directly into a shell command) with a Python heredoc that reads the value safely from the COVERAGE_CONTENT environment variable.

