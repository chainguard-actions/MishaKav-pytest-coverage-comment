<!-- markdownlint-disable -->

# Hardening Report: MishaKav--pytest-coverage-comment/v1.6.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **MishaKav--pytest-coverage-comment/v1.6.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All workflow files use mutable tag or branch references instead of pinned full-SHA commit hashes, making them vulnerable to supply-chain attacks if the referenced action is compromised or its tag is moved. Unpinned references found:
- live-test.yml: actions/checkout@v6, MishaKav/pytest-coverage-comment@main, schneegans/dynamic-badges-action@v1.7.0
- multiple-files.yml: actions/checkout@v6, MishaKav/pytest-coverage-comment@main (×4)
- test-branch.yml: actions/checkout@v6, MishaKav/pytest-coverage-comment@main
- update-coverage-in-readme.yml: actions/checkout@v6, actions/cache@v5, MishaKav/jest-coverage-comment@main, schneegans/dynamic-badges-action@v1.7.0
- update-coverage-on-readme.yml: actions/checkout@v6, MishaKav/pytest-coverage-comment@main, actions-js/push@master

Locations:

- `.github/workflows/live-test.yml:12`
- `.github/workflows/live-test.yml:15`
- `.github/workflows/live-test.yml:34`
- `.github/workflows/multiple-files.yml:11`
- `.github/workflows/multiple-files.yml:14`
- `.github/workflows/test-branch.yml:13`
- `.github/workflows/test-branch.yml:16`
- `.github/workflows/update-coverage-in-readme.yml:18`
- `.github/workflows/update-coverage-in-readme.yml:22`
- `.github/workflows/update-coverage-in-readme.yml:33`
- `.github/workflows/update-coverage-in-readme.yml:57`
- `.github/workflows/update-coverage-on-readme.yml:14`
- `.github/workflows/update-coverage-on-readme.yml:22`
- `.github/workflows/update-coverage-on-readme.yml:36`

### script-injection (severity: high)

Multiple run: blocks directly interpolate ${{ steps.*.outputs.* }} expressions inside shell commands (rule a). These values flow through YAML template substitution before the shell processes them, allowing a malicious step output containing shell metacharacters to execute arbitrary commands. Affected steps:
- live-test.yml 'Check the output coverage': echo "Coverage Percentage - ${{ steps.coverageComment.outputs.coverage }}" and similar lines
- test-branch.yml 'Check the output coverage': echo "Coverage Report - ${{ steps.coverageComment.outputs.coverage }}" and similar lines
- update-coverage-in-readme.yml 'Check the output coverage': echo "Coverage Percentage - ${{ steps.coverageComment.outputs.coverage }}" and many similar lines
- update-coverage-on-readme.yml 'Update Readme with Coverage Html': sed -i '...' with ${{ steps.coverageComment.outputs.content }} interpolated directly into the shell command

Locations:

- `.github/workflows/live-test.yml:23`
- `.github/workflows/test-branch.yml:22`
- `.github/workflows/update-coverage-in-readme.yml:42`
- `.github/workflows/update-coverage-on-readme.yml:28`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all 5 workflow files:

1. live-test.yml: Pinned actions/checkout@v6, MishaKav/pytest-coverage-comment@main, schneegans/dynamic-badges-action@v1.7.0 to full SHAs. Moved all ${{ steps.coverageComment.outputs.* }} expressions from run: block to env: block.

2. multiple-files.yml: Pinned actions/checkout@v6 and all 4 instances of MishaKav/pytest-coverage-comment@main to full SHAs. No script injection in this file.

3. test-branch.yml: Pinned actions/checkout@v6 and MishaKav/pytest-coverage-comment@main to full SHAs. Moved all ${{ steps.coverageComment.outputs.* }} expressions from run: block to env: block.

4. update-coverage-in-readme.yml: Pinned actions/checkout@v6, actions/cache@v5, MishaKav/jest-coverage-comment@main, and schneegans/dynamic-badges-action@v1.7.0 to full SHAs. Moved all ${{ steps.coverageComment.outputs.* }} expressions from run: block to env: block.

5. update-coverage-on-readme.yml: Pinned actions/checkout@v6, MishaKav/pytest-coverage-comment@main, and actions-js/push@master to full SHAs. Replaced the dangerous sed command that interpolated ${{ steps.coverageComment.outputs.content }} directly into shell with a Python script that reads the value from an env: variable (COVERAGE_CONTENT) safely.

