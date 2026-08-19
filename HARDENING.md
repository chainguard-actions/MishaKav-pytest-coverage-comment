<!-- markdownlint-disable -->

# Hardening Report: MishaKav--pytest-coverage-comment/v1.7.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **MishaKav--pytest-coverage-comment/v1.7.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple workflow run: blocks directly interpolate ${{ steps.coverageComment.outputs.* }} expressions (steps.*.outputs.* context) inside shell commands without routing through env: variables. Sub-rule (a): any ${{ ... }} expression inside a run: block is a script-injection risk because YAML template substitution happens before the shell ever sees the value. Offending lines include: `echo "Coverage Percentage - ${{ steps.coverageComment.outputs.coverage }}"` and similar in live-test.yml; `echo "Coverage Report - ${{ steps.coverageComment.outputs.coverage }}"` in test-branch.yml; multiple echo lines in update-coverage-in-readme.yml; and `sed -i '.../${{ steps.coverageComment.outputs.content }}...'` in update-coverage-on-readme.yml.

Locations:

- `.github/workflows/live-test.yml:24`
- `.github/workflows/test-branch.yml:20`
- `.github/workflows/update-coverage-in-readme.yml:43`
- `.github/workflows/update-coverage-on-readme.yml:30`

### unpinned-uses (severity: high)

All workflow files reference actions using mutable tags or branch names instead of pinned 40-character commit SHAs, making them vulnerable to supply-chain attacks. Failing references include: actions/checkout@v6, MishaKav/pytest-coverage-comment@main, schneegans/dynamic-badges-action@v1.7.0, actions/cache@v5, MishaKav/jest-coverage-comment@main, actions-js/push@master, actions/publish-immutable-action@v0.0.4.

Locations:

- `.github/workflows/live-test.yml:14`
- `.github/workflows/live-test.yml:18`
- `.github/workflows/live-test.yml:37`
- `.github/workflows/multiple-files.yml:13`
- `.github/workflows/multiple-files.yml:17`
- `.github/workflows/multiple-files.yml:24`
- `.github/workflows/multiple-files.yml:31`
- `.github/workflows/multiple-files.yml:38`
- `.github/workflows/publish-immutable-actions.yml:12`
- `.github/workflows/publish-immutable-actions.yml:13`
- `.github/workflows/test-branch.yml:14`
- `.github/workflows/test-branch.yml:18`
- `.github/workflows/update-coverage-in-readme.yml:18`
- `.github/workflows/update-coverage-in-readme.yml:23`
- `.github/workflows/update-coverage-in-readme.yml:38`
- `.github/workflows/update-coverage-in-readme.yml:55`
- `.github/workflows/update-coverage-on-readme.yml:14`
- `.github/workflows/update-coverage-on-readme.yml:24`
- `.github/workflows/update-coverage-on-readme.yml:36`
- `.github/workflows/update-main-version.yml:21`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed script-injection in live-test.yml, test-branch.yml, update-coverage-in-readme.yml, and update-coverage-on-readme.yml by moving all ${{ steps.coverageComment.outputs.* }} expressions from run: blocks into env: blocks, with shell scripts referencing plain env vars. Fixed unpinned-uses across all 6 workflow files by pinning all action references to full 40-character commit SHAs: actions/checkout@v6→d23441a48e516b6c34aea4fa41551a30e30af803, MishaKav/pytest-coverage-comment@main→09c28b6767e12a048e0b23e821ed03133ee48a1a, schneegans/dynamic-badges-action@v1.7.0→e9a478b16159b4d31420099ba146cdc50f134483, actions/cache@v5→caa296126883cff596d87d8935842f9db880ef25, MishaKav/jest-coverage-comment@main→642ef024cc554a34b7082cea12c7bf63575ae151, actions-js/push@master→968f4695ca558093eadb24ad83cc5891f47e0cdc, actions/publish-immutable-action@v0.0.4→4bc8754ffc40f27910afb20287dbbbb675a4e978. Original tag/branch names preserved as inline comments.

