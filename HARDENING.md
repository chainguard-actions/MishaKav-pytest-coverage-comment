<!-- markdownlint-disable -->

# Hardening Report: MishaKav--pytest-coverage-comment/v1.5.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **MishaKav--pytest-coverage-comment/v1.5.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Multiple `run:` blocks directly interpolate `${{ steps.coverageComment.outputs.* }}` expressions inside shell commands. The `steps.*.outputs.*` context flows through YAML template substitution before the shell sees it, allowing an attacker-controlled output value to inject shell metacharacters. Offending lines include: `echo "Coverage Percentage - ${{ steps.coverageComment.outputs.coverage }}"`, `echo "Coverage Color - ${{ steps.coverageComment.outputs.color }}"`, etc. These should be moved to `env:` variables and double-quoted in the shell.

Locations:

- `.github/workflows/live-test.yml:24`
- `.github/workflows/test-branch.yml:22`
- `.github/workflows/update-coverage-on-readme.yml:32`

### unpinned-uses (severity: high)

Multiple workflow files reference actions using mutable tags or branch names instead of immutable 40-character commit SHAs, making them vulnerable to supply-chain attacks if the referenced tag or branch is moved. Failing references include: `actions/checkout@v6` (all 4 files), `MishaKav/pytest-coverage-comment@main` (all 4 files), `schneegans/dynamic-badges-action@v1.7.0` (live-test.yml), `actions-js/push@master` (update-coverage-on-readme.yml). All should be pinned to full SHA digests.

Locations:

- `.github/workflows/live-test.yml:14`
- `.github/workflows/multiple-files.yml:13`
- `.github/workflows/test-branch.yml:14`
- `.github/workflows/update-coverage-on-readme.yml:15`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed script-injection in live-test.yml, test-branch.yml, and update-coverage-on-readme.yml by moving all ${{ steps.coverageComment.outputs.* }} expressions from run: shell commands into env: blocks, referencing them as plain environment variables. Fixed unpinned-uses in all four workflow files: actions/checkout@v6 → SHA d23441a48e516b6c34aea4fa41551a30e30af803, MishaKav/pytest-coverage-comment@main → SHA 09c28b6767e12a048e0b23e821ed03133ee48a1a, schneegans/dynamic-badges-action@v1.7.0 → SHA e9a478b16159b4d31420099ba146cdc50f134483, actions-js/push@master → SHA 968f4695ca558093eadb24ad83cc5891f47e0cdc. All SHAs were resolved via lookup_action_sha.

