<!-- markdownlint-disable -->

# Hardening Report: MishaKav--pytest-coverage-comment/v1.7.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **MishaKav--pytest-coverage-comment/v1.7.2** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): ${{ ... }} expressions from steps.*.outputs.* are directly interpolated inside run: shell command strings. These values flow through YAML template substitution before the shell sees them, enabling script injection. In update-coverage-on-readme.yml this is especially dangerous: `${{ steps.coverageComment.outputs.content }}` is embedded directly in a `sed` command, allowing arbitrary shell metacharacters from the action's output to be executed. In live-test.yml, test-branch.yml, and update-coverage-in-readme.yml, `${{ steps.coverageComment.outputs.* }}` values are echoed directly in run: blocks. Fix: move values into env: vars and double-quote the shell expansions.

Locations:

- `.github/workflows/live-test.yml:25`
- `.github/workflows/test-branch.yml:22`
- `.github/workflows/update-coverage-in-readme.yml:42`
- `.github/workflows/update-coverage-on-readme.yml:30`

### unpinned-uses (severity: high)

Multiple workflow files reference actions using mutable tags, branch names, or version strings instead of pinned 40-character commit SHAs. This exposes the workflow to supply-chain attacks if the referenced tag or branch is moved or compromised. Unpinned references found: live-test.yml — actions/checkout@v6 (line 15), MishaKav/pytest-coverage-comment@main (line 19), schneegans/dynamic-badges-action@v1.8.0 (line 36); multiple-files.yml — actions/checkout@v6 (line 14), MishaKav/pytest-coverage-comment@main (lines 17, 24, 31, 38); test-branch.yml — actions/checkout@v6 (line 15), MishaKav/pytest-coverage-comment@main (line 19); update-coverage-in-readme.yml — actions/checkout@v6 (line 20), actions/cache@v5 (line 23), MishaKav/jest-coverage-comment@main (line 35), schneegans/dynamic-badges-action@v1.8.0 (line 55); update-coverage-on-readme.yml — actions/checkout@v6 (line 15), MishaKav/pytest-coverage-comment@main (line 22), actions-js/push@master (line 35); update-main-version.yml — actions/checkout@v6 (line 21).

Locations:

- `.github/workflows/live-test.yml:15`
- `.github/workflows/live-test.yml:19`
- `.github/workflows/live-test.yml:36`
- `.github/workflows/multiple-files.yml:14`
- `.github/workflows/multiple-files.yml:17`
- `.github/workflows/multiple-files.yml:24`
- `.github/workflows/multiple-files.yml:31`
- `.github/workflows/multiple-files.yml:38`
- `.github/workflows/test-branch.yml:15`
- `.github/workflows/test-branch.yml:19`
- `.github/workflows/update-coverage-in-readme.yml:20`
- `.github/workflows/update-coverage-in-readme.yml:23`
- `.github/workflows/update-coverage-in-readme.yml:35`
- `.github/workflows/update-coverage-in-readme.yml:55`
- `.github/workflows/update-coverage-on-readme.yml:15`
- `.github/workflows/update-coverage-on-readme.yml:22`
- `.github/workflows/update-coverage-on-readme.yml:35`
- `.github/workflows/update-main-version.yml:21`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all 6 workflow files:

**unpinned-uses**: Pinned all action references to full 40-char commit SHAs:
- actions/checkout@v6 → @d23441a48e516b6c34aea4fa41551a30e30af803 (all files)
- MishaKav/pytest-coverage-comment@main → @09c28b6767e12a048e0b23e821ed03133ee48a1a (live-test.yml, multiple-files.yml, test-branch.yml, update-coverage-on-readme.yml)
- schneegans/dynamic-badges-action@v1.8.0 → @0e50b8bad39e7e1afd3e4e9c2b7dd145fad07501 (live-test.yml, update-coverage-in-readme.yml)
- actions/cache@v5 → @caa296126883cff596d87d8935842f9db880ef25 (update-coverage-in-readme.yml)
- MishaKav/jest-coverage-comment@main → @642ef024cc554a34b7082cea12c7bf63575ae151 (update-coverage-in-readme.yml)
- actions-js/push@master → @968f4695ca558093eadb24ad83cc5891f47e0cdc (update-coverage-on-readme.yml)

**script-injection**: Moved all ${{ steps.coverageComment.outputs.* }} expressions from run: shell strings into env: blocks, referencing them as plain $VAR_NAME shell variables. The most critical fix was in update-coverage-on-readme.yml where the output was embedded directly in a sed command — now uses $COVERAGE_CONTENT env var instead.

