<!-- markdownlint-disable -->

# Hardening Report: MishaKav--pytest-coverage-comment/v1.11.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **MishaKav--pytest-coverage-comment/v1.11.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple `run:` blocks directly interpolate `${{ steps.*.outputs.* }}` expressions inside shell commands (sub-rule a). These expressions are substituted by the YAML template engine before the shell processes them, allowing attacker-influenced step output values to inject shell metacharacters. Affected commands include `echo "... ${{ steps.coverageComment.outputs.coverage }}"` and `sed -i '...${{ steps.coverageComment.outputs.content }}...'`. All such expressions should be routed through `env:` variables and double-quoted in the shell instead.

Locations:

- `.github/workflows/live-test.yml:26`
- `.github/workflows/test-branch.yml:22`
- `.github/workflows/update-coverage-in-readme.yml:46`
- `.github/workflows/update-coverage-on-readme.yml:30`

### unpinned-uses (severity: high)

All `uses:` references across every workflow file use mutable tags or branch names instead of immutable 40-character SHA commit hashes, exposing the workflows to supply-chain attacks. Failing references include: `actions/checkout@v7`, `MishaKav/pytest-coverage-comment@main`, `schneegans/dynamic-badges-action@v1.9.0`, `actions/cache@v6`, `MishaKav/jest-coverage-comment@main`, `actions-js/push@master`.

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
- `.github/workflows/update-coverage-in-readme.yml:18`
- `.github/workflows/update-coverage-in-readme.yml:22`
- `.github/workflows/update-coverage-in-readme.yml:36`
- `.github/workflows/update-coverage-in-readme.yml:80`
- `.github/workflows/update-coverage-on-readme.yml:16`
- `.github/workflows/update-coverage-on-readme.yml:23`
- `.github/workflows/update-coverage-on-readme.yml:37`
- `.github/workflows/update-main-version.yml:19`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed script-injection in live-test.yml (line 26), test-branch.yml (line 22), update-coverage-in-readme.yml (line 46), and update-coverage-on-readme.yml (line 30) by moving all ${{ steps.*.outputs.* }} expressions into env: blocks and referencing them as plain shell variables. Fixed unpinned-uses across all 6 workflow files: actions/checkout@v7 → SHA 3d3c42e5, MishaKav/pytest-coverage-comment@main → SHA 09c28b67, schneegans/dynamic-badges-action@v1.9.0 → SHA 28b0fa8b, actions/cache@v6 → SHA 55cc8345, MishaKav/jest-coverage-comment@main → SHA 642ef024, actions-js/push@master → SHA 968f4695. Original tags preserved as inline comments.

