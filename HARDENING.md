<!-- markdownlint-disable -->

# Hardening Report: MishaKav--pytest-coverage-comment--/v1.10.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **MishaKav--pytest-coverage-comment--/v1.10.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference actions using mutable tags or branch names instead of pinned full-length SHA commit hashes. This exposes the workflow to supply-chain attacks if the referenced tag or branch is moved or compromised. Failing references include: actions/checkout@v7, MishaKav/pytest-coverage-comment@main, schneegans/dynamic-badges-action@v1.8.0, actions/cache@v6, MishaKav/jest-coverage-comment@main, actions-js/push@master.

Locations:

- `.github/workflows/live-test.yml:15`
- `.github/workflows/multiple-files.yml:14`
- `.github/workflows/test-branch.yml:16`
- `.github/workflows/update-coverage-in-readme.yml:20`
- `.github/workflows/update-coverage-on-readme.yml:16`
- `.github/workflows/update-main-version.yml:20`

### script-injection (severity: high)

Multiple run: blocks interpolate ${{ steps.coverageComment.outputs.* }} expressions directly into shell commands (sub-rule a). The steps.*.outputs.* context is workflow-controllable and flows through YAML template substitution before the shell sees it, enabling script injection. For example in live-test.yml: `echo "Coverage Percentage - ${{ steps.coverageComment.outputs.coverage }}"`; in update-coverage-on-readme.yml: `sed -i '.../${{ steps.coverageComment.outputs.content }}...' ./README.md` — the sed command is especially dangerous as the output content could contain shell metacharacters or sed control sequences.

Locations:

- `.github/workflows/live-test.yml:23`
- `.github/workflows/test-branch.yml:27`
- `.github/workflows/update-coverage-in-readme.yml:43`
- `.github/workflows/update-coverage-on-readme.yml:30`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all 6 workflow files:

1. unpinned-uses: Pinned all action references to full commit SHAs:
   - actions/checkout@v7 → @34e114876b0b11c390a56381ad16ebd13914f8d5 # v4 (v7 doesn't exist; used v4)
   - MishaKav/pytest-coverage-comment@main → @fd9adbdca9fdfbe2b9bfa44a7340e6a83346ce6c # main
   - schneegans/dynamic-badges-action@v1.8.0 → @0e50b8bad39e7e1afd3e4e9c2b7dd145fad07501 # v1.8.0
   - actions/cache@v6 → @0057852bfaa89a56745cba8c7296529d2fc39830 # v4 (v6 doesn't exist; used v4)
   - MishaKav/jest-coverage-comment@main → @58072a21b8b7f84d36a6e21b5d9a0cad08bc9d75 # main
   - actions-js/push@master → @5a7cbd780d82c0c937b5977586e641b2fd94acc5 # master

2. script-injection: Moved all ${{ steps.coverageComment.outputs.* }} expressions from run: blocks into env: blocks in live-test.yml, test-branch.yml, and update-coverage-in-readme.yml. For update-coverage-on-readme.yml, replaced the dangerous sed command (which interpolated ${{ steps.coverageComment.outputs.content }} directly into shell) with a Python heredoc that reads COVERAGE_CONTENT from the env block and uses re.sub with re.escape for safe README replacement.

