<!-- markdownlint-disable -->

# Hardening Report: MishaKav--pytest-coverage-comment/v1.12.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **MishaKav--pytest-coverage-comment/v1.12.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All `uses:` references across every workflow file use mutable tags or branch names instead of pinned 40-character SHA commit hashes, making the workflows vulnerable to supply-chain attacks if any referenced action is compromised or its tag is moved.

Failing references:
- live-test.yml: `actions/checkout@v7`, `MishaKav/pytest-coverage-comment@main`, `schneegans/dynamic-badges-action@v1.9.0`
- multiple-files.yml: `actions/checkout@v7`, `MishaKav/pytest-coverage-comment@main` (×4)
- test-branch.yml: `actions/checkout@v7`, `MishaKav/pytest-coverage-comment@main`
- update-coverage-in-readme.yml: `actions/checkout@v7`, `actions/cache@v6`, `MishaKav/jest-coverage-comment@main`, `schneegans/dynamic-badges-action@v1.9.0`
- update-coverage-on-readme.yml: `actions/checkout@v7`, `MishaKav/pytest-coverage-comment@main`, `actions-js/push@master`
- update-main-version.yml: `actions/checkout@v7`

Locations:

- `.github/workflows/live-test.yml:14`
- `.github/workflows/live-test.yml:18`
- `.github/workflows/live-test.yml:36`
- `.github/workflows/multiple-files.yml:13`
- `.github/workflows/multiple-files.yml:17`
- `.github/workflows/multiple-files.yml:26`
- `.github/workflows/multiple-files.yml:33`
- `.github/workflows/multiple-files.yml:41`
- `.github/workflows/test-branch.yml:14`
- `.github/workflows/test-branch.yml:18`
- `.github/workflows/update-coverage-in-readme.yml:18`
- `.github/workflows/update-coverage-in-readme.yml:21`
- `.github/workflows/update-coverage-in-readme.yml:40`
- `.github/workflows/update-coverage-in-readme.yml:72`
- `.github/workflows/update-coverage-on-readme.yml:14`
- `.github/workflows/update-coverage-on-readme.yml:22`
- `.github/workflows/update-coverage-on-readme.yml:33`
- `.github/workflows/update-main-version.yml:21`

### script-injection (severity: high)

Multiple `run:` blocks directly interpolate `${{ ... }}` expressions inside shell commands (rule a). Even though these reference `steps.*.outputs.*` rather than `github.*` or `inputs.*`, any `${{ }}` expression inside a `run:` block is a script-injection risk because the value is substituted by the template engine before the shell ever sees it — allowing shell metacharacters in the value to be interpreted.

- live-test.yml (~line 25): `echo "Coverage Percentage - ${{ steps.coverageComment.outputs.coverage }}"` and multiple similar lines
- test-branch.yml (~line 22): `echo "Coverage Report - ${{ steps.coverageComment.outputs.coverage }}"` and similar lines
- update-coverage-in-readme.yml (~line 48): `echo "Coverage Percentage - ${{ steps.coverageComment.outputs.coverage }}"` and many similar lines
- update-coverage-on-readme.yml (~line 28): `sed -i '...${{ steps.coverageComment.outputs.content }}...' ./README.md` — particularly dangerous as the output is interpolated directly into a sed command

Locations:

- `.github/workflows/live-test.yml:25`
- `.github/workflows/test-branch.yml:22`
- `.github/workflows/update-coverage-in-readme.yml:48`
- `.github/workflows/update-coverage-on-readme.yml:28`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all 6 workflow files:

**unpinned-uses**: Pinned all action references to full 40-char SHAs:
- actions/checkout@v7 → @3d3c42e5aac5ba805825da76410c181273ba90b1
- MishaKav/pytest-coverage-comment@main → @1fcf8a5d3ce4e9315837759203b3b7512f333e81
- schneegans/dynamic-badges-action@v1.9.0 → @28b0fa8bdeb46170ac397105ece0c1fe58f68910
- actions/cache@v6 → @55cc8345863c7cc4c66a329aec7e433d2d1c52a9
- MishaKav/jest-coverage-comment@main → @642ef024cc554a34b7082cea12c7bf63575ae151
- actions-js/push@master → @968f4695ca558093eadb24ad83cc5891f47e0cdc

**script-injection**: Moved all ${{ steps.coverageComment.outputs.* }} expressions from run: blocks into env: blocks in live-test.yml, test-branch.yml, update-coverage-in-readme.yml, and update-coverage-on-readme.yml. The most dangerous case (sed command in update-coverage-on-readme.yml) was fixed by moving the content output to an env var COVERAGE_CONTENT and referencing it as "$COVERAGE_CONTENT" in the shell.

