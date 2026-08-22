<!-- markdownlint-disable -->

# Hardening Report: MishaKav--pytest-coverage-comment/v1.11.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **MishaKav--pytest-coverage-comment/v1.11.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Multiple run: blocks directly interpolate ${{ steps.*.outputs.* }} expressions inside shell commands. Although steps outputs are not directly attacker-controlled, any ${{ ... }} expression interpolated directly into a run: block is a script-injection risk because the value is substituted by the YAML template engine before the shell ever sees it, bypassing shell quoting. Offending lines include: live-test.yml — `echo "Coverage Percentage - ${{ steps.coverageComment.outputs.coverage }}"`; test-branch.yml — `echo "Coverage Report - ${{ steps.coverageComment.outputs.coverage }}"`; update-coverage-in-readme.yml — `echo "Coverage Percentage - ${{ steps.coverageComment.outputs.coverage }}"`; update-coverage-on-readme.yml — `sed -i '...\${{ steps.coverageComment.outputs.content }}...' ./README.md` (particularly dangerous as the output is embedded in a sed command). All should be moved to env: variables and referenced as $ENV_VAR inside the run: block.

Locations:

- `.github/workflows/live-test.yml:26`
- `.github/workflows/test-branch.yml:27`
- `.github/workflows/update-coverage-in-readme.yml:48`
- `.github/workflows/update-coverage-on-readme.yml:30`

### unpinned-uses (severity: high)

Every uses: reference across all workflow files uses a mutable tag or branch name instead of a pinned 40-character commit SHA. This exposes the workflow to supply-chain attacks where a compromised or malicious tag update could execute arbitrary code in the runner. Failing references include: actions/checkout@v7, MishaKav/pytest-coverage-comment@main, schneegans/dynamic-badges-action@v1.9.0, actions/cache@v6, MishaKav/jest-coverage-comment@main, actions-js/push@master. All should be pinned to their full SHA digest (e.g. actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4).

Locations:

- `.github/workflows/live-test.yml:15`
- `.github/workflows/live-test.yml:19`
- `.github/workflows/live-test.yml:40`
- `.github/workflows/multiple-files.yml:13`
- `.github/workflows/multiple-files.yml:17`
- `.github/workflows/multiple-files.yml:24`
- `.github/workflows/multiple-files.yml:31`
- `.github/workflows/multiple-files.yml:38`
- `.github/workflows/test-branch.yml:16`
- `.github/workflows/test-branch.yml:20`
- `.github/workflows/update-coverage-in-readme.yml:18`
- `.github/workflows/update-coverage-in-readme.yml:22`
- `.github/workflows/update-coverage-in-readme.yml:42`
- `.github/workflows/update-coverage-in-readme.yml:72`
- `.github/workflows/update-coverage-on-readme.yml:15`
- `.github/workflows/update-coverage-on-readme.yml:23`
- `.github/workflows/update-coverage-on-readme.yml:35`
- `.github/workflows/update-main-version.yml:21`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed script-injection in 4 workflow files by moving all ${{ steps.*.outputs.* }} expressions from run: shell blocks into env: blocks, then referencing them as plain $ENV_VAR variables. Fixed unpinned-uses in all 6 workflow files by pinning: actions/checkout@v7 → SHA 3d3c42e5, MishaKav/pytest-coverage-comment@main → SHA d46d9d59, schneegans/dynamic-badges-action@v1.9.0 → SHA 28b0fa8b, actions/cache@v6 → SHA 55cc8345, MishaKav/jest-coverage-comment@main → SHA 642ef024, actions-js/push@master → SHA 968f4695. All SHAs were resolved via lookup_action_sha.

