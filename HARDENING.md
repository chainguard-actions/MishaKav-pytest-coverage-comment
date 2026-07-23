<!-- markdownlint-disable -->

# Hardening Report: MishaKav--pytest-coverage-comment/v1.9.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **MishaKav--pytest-coverage-comment/v1.9.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All workflow files reference actions using mutable tags or branch names instead of immutable 40-character SHA commit hashes. This exposes the workflow to supply-chain attacks if the referenced tag or branch is moved to a malicious commit. Affected references include: actions/checkout@v7, MishaKav/pytest-coverage-comment@main, schneegans/dynamic-badges-action@v1.8.0, actions/cache@v6, MishaKav/jest-coverage-comment@main, actions-js/push@master.

Locations:

- `.github/workflows/live-test.yml:15`
- `.github/workflows/live-test.yml:19`
- `.github/workflows/live-test.yml:37`
- `.github/workflows/multiple-files.yml:14`
- `.github/workflows/multiple-files.yml:17`
- `.github/workflows/multiple-files.yml:24`
- `.github/workflows/multiple-files.yml:31`
- `.github/workflows/multiple-files.yml:38`
- `.github/workflows/test-branch.yml:15`
- `.github/workflows/test-branch.yml:19`
- `.github/workflows/update-coverage-in-readme.yml:17`
- `.github/workflows/update-coverage-in-readme.yml:20`
- `.github/workflows/update-coverage-in-readme.yml:38`
- `.github/workflows/update-coverage-in-readme.yml:62`
- `.github/workflows/update-coverage-on-readme.yml:14`
- `.github/workflows/update-coverage-on-readme.yml:20`
- `.github/workflows/update-coverage-on-readme.yml:34`
- `.github/workflows/update-main-version.yml:18`

### script-injection (severity: high)

Sub-rule (a): Multiple run: blocks directly interpolate ${{ steps.coverageComment.outputs.* }} expressions inside shell commands. The steps.*.outputs.* context flows through YAML template substitution before the shell processes it, meaning any special characters or newlines in the output value are interpreted by the shell. For example: `echo "Coverage Percentage - ${{ steps.coverageComment.outputs.coverage }}"` and `sed -i '.../${{ steps.coverageComment.outputs.content }}/...' ./README.md`. An attacker who can influence the coverage report output could inject shell metacharacters. All ${{ ... }} expressions must be moved to env: variables and those env vars must be double-quoted in the shell script.

Locations:

- `.github/workflows/live-test.yml:26`
- `.github/workflows/live-test.yml:27`
- `.github/workflows/live-test.yml:28`
- `.github/workflows/live-test.yml:30`
- `.github/workflows/live-test.yml:32`
- `.github/workflows/live-test.yml:33`
- `.github/workflows/live-test.yml:34`
- `.github/workflows/live-test.yml:35`
- `.github/workflows/live-test.yml:36`
- `.github/workflows/live-test.yml:38`
- `.github/workflows/test-branch.yml:23`
- `.github/workflows/test-branch.yml:24`
- `.github/workflows/test-branch.yml:25`
- `.github/workflows/update-coverage-in-readme.yml:43`
- `.github/workflows/update-coverage-in-readme.yml:44`
- `.github/workflows/update-coverage-in-readme.yml:45`
- `.github/workflows/update-coverage-in-readme.yml:47`
- `.github/workflows/update-coverage-in-readme.yml:48`
- `.github/workflows/update-coverage-in-readme.yml:49`
- `.github/workflows/update-coverage-in-readme.yml:50`
- `.github/workflows/update-coverage-in-readme.yml:51`
- `.github/workflows/update-coverage-on-readme.yml:28`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all unpinned action references by resolving them to full 40-character SHA commit hashes using lookup_action_sha: actions/checkout@v7→3d3c42e5..., MishaKav/pytest-coverage-comment@main→fd9adbdc..., schneegans/dynamic-badges-action@v1.8.0→0e50b8ba..., actions/cache@v6→55cc8345..., MishaKav/jest-coverage-comment@main→58072a21..., actions-js/push@master→5a7cbd78.... Fixed script injection in live-test.yml, test-branch.yml, update-coverage-in-readme.yml, and update-coverage-on-readme.yml by moving all ${{ steps.coverageComment.outputs.* }} expressions from run: shell commands into env: blocks and referencing them as plain $VAR_NAME environment variables in the shell scripts.

