<!-- markdownlint-disable -->

# Hardening Report: MishaKav--pytest-coverage-comment/v1.12.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **MishaKav--pytest-coverage-comment/v1.12.2** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All `uses:` references across all workflow files use mutable tags or branch names instead of pinned 40-character SHA commits, making the workflows vulnerable to supply-chain attacks if the referenced action or tag is compromised or moved. Failing references include: `actions/checkout@v7`, `MishaKav/pytest-coverage-comment@main`, `schneegans/dynamic-badges-action@v1.9.0`, `actions/cache@v6`, `MishaKav/jest-coverage-comment@main`, `actions-js/push@master`.

Locations:

- `.github/workflows/live-test.yml:15`
- `.github/workflows/live-test.yml:18`
- `.github/workflows/live-test.yml:37`
- `.github/workflows/multiple-files.yml:13`
- `.github/workflows/multiple-files.yml:17`
- `.github/workflows/multiple-files.yml:23`
- `.github/workflows/multiple-files.yml:29`
- `.github/workflows/multiple-files.yml:35`
- `.github/workflows/test-branch.yml:15`
- `.github/workflows/test-branch.yml:18`
- `.github/workflows/update-coverage-in-readme.yml:20`
- `.github/workflows/update-coverage-in-readme.yml:23`
- `.github/workflows/update-coverage-in-readme.yml:38`
- `.github/workflows/update-coverage-in-readme.yml:57`
- `.github/workflows/update-coverage-on-readme.yml:15`
- `.github/workflows/update-coverage-on-readme.yml:22`
- `.github/workflows/update-coverage-on-readme.yml:33`
- `.github/workflows/update-main-version.yml:18`

### script-injection (severity: high)

Multiple `run:` blocks directly interpolate `${{ ... }}` expressions inside shell commands (sub-rule a). The `steps.*` context is workflow-controllable and is substituted by the YAML template engine before the shell sees it, allowing injection of shell metacharacters. In `update-coverage-on-readme.yml`, `${{ steps.coverageComment.outputs.content }}` is interpolated directly inside a `sed` command — the action output could contain arbitrary content that breaks out of the sed argument. In `live-test.yml` and `test-branch.yml`, multiple `echo` commands interpolate `${{ steps.coverageComment.outputs.* }}` values directly. In `update-coverage-in-readme.yml`, multiple `echo` commands interpolate `${{ steps.coverageComment.outputs.* }}` values. All these should be routed through env vars and double-quoted instead.

Locations:

- `.github/workflows/live-test.yml:25`
- `.github/workflows/test-branch.yml:22`
- `.github/workflows/update-coverage-in-readme.yml:45`
- `.github/workflows/update-coverage-on-readme.yml:29`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all 18 unpinned `uses:` references across 5 workflow files by pinning to full 40-character SHA commits (with tag comments for readability). Fixed all 4 script-injection locations by moving `${{ steps.coverageComment.outputs.* }}` expressions from inline shell interpolation into `env:` blocks, then referencing them as plain environment variables in the `run:` scripts. Actions pinned: actions/checkout@v7→SHA, MishaKav/pytest-coverage-comment@main→SHA, schneegans/dynamic-badges-action@v1.9.0→SHA, actions/cache@v6→SHA, MishaKav/jest-coverage-comment@main→SHA, actions-js/push@master→SHA.

