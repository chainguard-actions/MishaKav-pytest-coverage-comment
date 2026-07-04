<!-- markdownlint-disable -->

# Hardening Report: MishaKav--pytest-coverage-comment--/v1.9.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **MishaKav--pytest-coverage-comment--/v1.9.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference actions using mutable tags/branches instead of full 40-character commit SHAs. Unpinned refs are vulnerable to supply-chain attacks if the referenced tag or branch is moved. Failing references include: actions/checkout@v7, MishaKav/pytest-coverage-comment@main, schneegans/dynamic-badges-action@v1.8.0, actions/cache@v6, MishaKav/jest-coverage-comment@main, actions-js/push@master.

Locations:

- `.github/workflows/live-test.yml:14`
- `.github/workflows/live-test.yml:19`
- `.github/workflows/live-test.yml:35`
- `.github/workflows/multiple-files.yml:13`
- `.github/workflows/multiple-files.yml:17`
- `.github/workflows/test-branch.yml:14`
- `.github/workflows/test-branch.yml:19`
- `.github/workflows/update-coverage-in-readme.yml:19`
- `.github/workflows/update-coverage-in-readme.yml:23`
- `.github/workflows/update-coverage-in-readme.yml:37`
- `.github/workflows/update-coverage-in-readme.yml:57`
- `.github/workflows/update-coverage-on-readme.yml:16`
- `.github/workflows/update-coverage-on-readme.yml:23`
- `.github/workflows/update-coverage-on-readme.yml:37`
- `.github/workflows/update-main-version.yml:20`

### script-injection (severity: high)

Multiple run: blocks directly interpolate ${{ steps.*.outputs.* }} expressions inside shell commands (sub-rule a). Any ${{ ... }} expression interpolated directly in a run: block is a script-injection risk because the value is substituted into the shell command string before the shell parses it, allowing shell metacharacters to be interpreted. In update-coverage-on-readme.yml, ${{ steps.coverageComment.outputs.content }} is passed directly to a sed command, which is especially dangerous as the output could contain shell metacharacters or newlines that alter the command.

Locations:

- `.github/workflows/live-test.yml:23`
- `.github/workflows/test-branch.yml:22`
- `.github/workflows/update-coverage-in-readme.yml:44`
- `.github/workflows/update-coverage-on-readme.yml:28`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all 15 unpinned action references across 6 workflow files by pinning to full 40-character commit SHAs (with tag comments for readability). Fixed all 4 script injection instances by moving ${{ steps.*.outputs.* }} expressions into env: blocks and referencing them as plain environment variables in shell scripts. The most dangerous case in update-coverage-on-readme.yml (content passed to sed) was replaced with a Python script that reads the value safely from an environment variable, eliminating the risk of shell metacharacter injection.

