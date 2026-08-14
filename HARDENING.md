<!-- markdownlint-disable -->

# Hardening Report: MishaKav--pytest-coverage-comment/v1.7.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **MishaKav--pytest-coverage-comment/v1.7.3** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple run: blocks directly interpolate ${{ steps.coverageComment.outputs.* }} expressions (steps.*.outputs.* context) into shell commands. These values flow through YAML template substitution before the shell processes them, allowing a malicious action output to inject arbitrary shell commands. The most dangerous instance is in update-coverage-on-readme.yml where ${{ steps.coverageComment.outputs.content }} is interpolated directly into a sed command. Sub-rule (a): direct expression interpolation in run: blocks.

Locations:

- `.github/workflows/live-test.yml:23`
- `.github/workflows/test-branch.yml:22`
- `.github/workflows/update-coverage-in-readme.yml:46`
- `.github/workflows/update-coverage-on-readme.yml:30`

### unpinned-uses (severity: high)

All workflow files reference actions using mutable tags or branch names instead of pinned 40-character SHA commit hashes, making them vulnerable to supply-chain attacks. Unpinned references found: actions/checkout@v6, MishaKav/pytest-coverage-comment@main, schneegans/dynamic-badges-action@v1.8.0, actions/cache@v5, MishaKav/jest-coverage-comment@main, actions-js/push@master.

Locations:

- `.github/workflows/live-test.yml:14`
- `.github/workflows/live-test.yml:18`
- `.github/workflows/live-test.yml:36`
- `.github/workflows/multiple-files.yml:13`
- `.github/workflows/multiple-files.yml:17`
- `.github/workflows/multiple-files.yml:24`
- `.github/workflows/multiple-files.yml:31`
- `.github/workflows/multiple-files.yml:38`
- `.github/workflows/test-branch.yml:14`
- `.github/workflows/test-branch.yml:18`
- `.github/workflows/update-coverage-in-readme.yml:19`
- `.github/workflows/update-coverage-in-readme.yml:23`
- `.github/workflows/update-coverage-in-readme.yml:37`
- `.github/workflows/update-coverage-in-readme.yml:72`
- `.github/workflows/update-coverage-on-readme.yml:14`
- `.github/workflows/update-coverage-on-readme.yml:20`
- `.github/workflows/update-coverage-on-readme.yml:38`
- `.github/workflows/update-main-version.yml:21`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all 6 workflow files:

1. **unpinned-uses**: Pinned all action references to full 40-char SHAs with tag comments:
   - actions/checkout@v6 → @df4cb1c069e1874edd31b4311f1884172cec0e10 # v6
   - MishaKav/pytest-coverage-comment@main → @fd9adbdca9fdfbe2b9bfa44a7340e6a83346ce6c # main
   - schneegans/dynamic-badges-action@v1.8.0 → @0e50b8bad39e7e1afd3e4e9c2b7dd145fad07501 # v1.8.0
   - actions/cache@v5 → @caa296126883cff596d87d8935842f9db880ef25 # v5
   - MishaKav/jest-coverage-comment@main → @58072a21b8b7f84d36a6e21b5d9a0cad08bc9d75 # main
   - actions-js/push@master → @5a7cbd780d82c0c937b5977586e641b2fd94acc5 # master

2. **script-injection**: Moved all ${{ steps.coverageComment.outputs.* }} expressions from run: blocks into env: blocks in live-test.yml, test-branch.yml, update-coverage-in-readme.yml, and update-coverage-on-readme.yml. The most dangerous case (sed command in update-coverage-on-readme.yml) now uses $COVERAGE_CONTENT env var instead of direct template interpolation.

