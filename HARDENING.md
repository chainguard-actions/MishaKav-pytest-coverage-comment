<!-- markdownlint-disable -->

# Hardening Report: MishaKav--pytest-coverage-comment/v1.11.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **MishaKav--pytest-coverage-comment/v1.11.2** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference actions using mutable tags (branch names, version tags) instead of immutable 40-character commit SHAs. This exposes the workflow to supply-chain attacks if the referenced tag is moved or the repository is compromised.

.github/workflows/live-test.yml:
  - actions/checkout@v7 (line 15)
  - MishaKav/pytest-coverage-comment@main (line 19)
  - schneegans/dynamic-badges-action@v1.9.0 (line 47)

.github/workflows/multiple-files.yml:
  - actions/checkout@v7 (line 14)
  - MishaKav/pytest-coverage-comment@main (lines 17, 24, 31, 38)

.github/workflows/test-branch.yml:
  - actions/checkout@v7 (line 16)
  - MishaKav/pytest-coverage-comment@main (line 20)

.github/workflows/update-coverage-in-readme.yml:
  - actions/checkout@v7 (line 19)
  - actions/cache@v6 (line 23)
  - MishaKav/jest-coverage-comment@main (line 40)
  - schneegans/dynamic-badges-action@v1.9.0 (line 68)

.github/workflows/update-coverage-on-readme.yml:
  - actions/checkout@v7 (line 16)
  - MishaKav/pytest-coverage-comment@main (line 24)
  - actions-js/push@master (line 36)

.github/workflows/update-main-version.yml:
  - actions/checkout@v7 (line 20)

Locations:

- `.github/workflows/live-test.yml:15`
- `.github/workflows/live-test.yml:19`
- `.github/workflows/live-test.yml:47`
- `.github/workflows/multiple-files.yml:14`
- `.github/workflows/multiple-files.yml:17`
- `.github/workflows/multiple-files.yml:24`
- `.github/workflows/multiple-files.yml:31`
- `.github/workflows/multiple-files.yml:38`
- `.github/workflows/test-branch.yml:16`
- `.github/workflows/test-branch.yml:20`
- `.github/workflows/update-coverage-in-readme.yml:19`
- `.github/workflows/update-coverage-in-readme.yml:23`
- `.github/workflows/update-coverage-in-readme.yml:40`
- `.github/workflows/update-coverage-in-readme.yml:68`
- `.github/workflows/update-coverage-on-readme.yml:16`
- `.github/workflows/update-coverage-on-readme.yml:24`
- `.github/workflows/update-coverage-on-readme.yml:36`
- `.github/workflows/update-main-version.yml:20`

### script-injection (severity: high)

Multiple run: blocks interpolate ${{ ... }} expressions directly into shell command strings (sub-rule a). The values come from steps.*.outputs.*, which are populated by third-party actions (MishaKav/pytest-coverage-comment@main, MishaKav/jest-coverage-comment@main) and could contain shell metacharacters or newlines that break out of the intended command context. These should be passed via env: variables and then referenced as double-quoted shell variables.

live-test.yml — 'Check the output coverage' step (line 25 run: block): e.g. `echo "Coverage Percentage - ${{ steps.coverageComment.outputs.coverage }}"`

test-branch.yml — 'Check the output coverage' step (line 27 run: block): e.g. `echo "Coverage Report - ${{ steps.coverageComment.outputs.coverage }}"`

update-coverage-in-readme.yml — 'Check the output coverage' step (line 46 run: block): e.g. `echo "Coverage Percentage - ${{ steps.coverageComment.outputs.coverage }}"`

update-coverage-on-readme.yml — 'Update Readme with Coverage Html' step (line 31 run: block): `sed -i '...c\...\n\${{ steps.coverageComment.outputs.content }}\n...' ./README.md`

Locations:

- `.github/workflows/live-test.yml:25`
- `.github/workflows/test-branch.yml:27`
- `.github/workflows/update-coverage-in-readme.yml:46`
- `.github/workflows/update-coverage-on-readme.yml:31`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all 6 workflow files:

1. Pinned all action references to full 40-char SHAs (with tag comments for readability):
   - actions/checkout@v7 → @3d3c42e5aac5ba805825da76410c181273ba90b1
   - MishaKav/pytest-coverage-comment@main → @ce64cfb4332195faa36733a0364dda8849109f70
   - schneegans/dynamic-badges-action@v1.9.0 → @28b0fa8bdeb46170ac397105ece0c1fe58f68910
   - actions/cache@v6 → @55cc8345863c7cc4c66a329aec7e433d2d1c52a9
   - MishaKav/jest-coverage-comment@main → @642ef024cc554a34b7082cea12c7bf63575ae151
   - actions-js/push@master → @968f4695ca558093eadb24ad83cc5891f47e0cdc

2. Fixed script injection in 4 workflow files by moving all ${{ steps.coverageComment.outputs.* }} expressions from run: shell strings into env: blocks, then referencing them as plain shell variables ($COVERAGE, $COLOR, etc.). The sed command in update-coverage-on-readme.yml was similarly fixed by moving the content output to an env var.

