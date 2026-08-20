<!-- markdownlint-disable -->

# Hardening Report: MishaKav--pytest-coverage-comment/v1.7.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **MishaKav--pytest-coverage-comment/v1.7.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All `uses:` references across all workflow files use mutable tags or branch names instead of full 40-character SHA commit digests, making the workflows vulnerable to supply-chain attacks if the referenced actions are compromised or their tags are moved.

Failing references:
- actions/checkout@v6 (used in live-test.yml, multiple-files.yml, test-branch.yml, update-coverage-in-readme.yml, update-coverage-on-readme.yml, update-main-version.yml)
- MishaKav/pytest-coverage-comment@main (used in live-test.yml, multiple-files.yml, test-branch.yml, update-coverage-on-readme.yml)
- schneegans/dynamic-badges-action@v1.7.0 (used in live-test.yml, update-coverage-in-readme.yml)
- actions/cache@v5 (used in update-coverage-in-readme.yml)
- MishaKav/jest-coverage-comment@main (used in update-coverage-in-readme.yml)
- actions-js/push@master (used in update-coverage-on-readme.yml)

Locations:

- `.github/workflows/live-test.yml:15`
- `.github/workflows/live-test.yml:19`
- `.github/workflows/live-test.yml:37`
- `.github/workflows/multiple-files.yml:13`
- `.github/workflows/multiple-files.yml:16`
- `.github/workflows/multiple-files.yml:24`
- `.github/workflows/multiple-files.yml:30`
- `.github/workflows/multiple-files.yml:36`
- `.github/workflows/test-branch.yml:16`
- `.github/workflows/test-branch.yml:19`
- `.github/workflows/update-coverage-in-readme.yml:18`
- `.github/workflows/update-coverage-in-readme.yml:22`
- `.github/workflows/update-coverage-in-readme.yml:41`
- `.github/workflows/update-coverage-in-readme.yml:71`
- `.github/workflows/update-coverage-on-readme.yml:16`
- `.github/workflows/update-coverage-on-readme.yml:23`
- `.github/workflows/update-coverage-on-readme.yml:35`
- `.github/workflows/update-main-version.yml:20`

### script-injection (severity: high)

Multiple `run:` blocks directly interpolate `${{ ... }}` expressions inside shell commands. Any `${{ }}` expression inside a `run:` block is a script injection risk because the value is substituted into the shell command string before the shell parses it, allowing shell metacharacters in the value to be interpreted.

(a) live-test.yml — 'Check the output coverage' step interpolates `${{ steps.coverageComment.outputs.coverage }}`, `${{ steps.coverageComment.outputs.color }}`, `${{ steps.coverageComment.outputs.coverageHtml }}`, `${{ steps.coverageComment.outputs.warnings }}`, `${{ steps.coverageComment.outputs.errors }}`, `${{ steps.coverageComment.outputs.failures }}`, `${{ steps.coverageComment.outputs.skipped }}`, `${{ steps.coverageComment.outputs.tests }}`, `${{ steps.coverageComment.outputs.time }}`, and `${{ steps.coverageComment.outputs.notSuccessTestInfo }}` directly into echo commands.

(a) test-branch.yml — 'Check the output coverage' step interpolates `${{ steps.coverageComment.outputs.coverage }}`, `${{ steps.coverageComment.outputs.color }}`, and `${{ steps.coverageComment.outputs.coverageHtml }}` directly into echo commands.

(a) update-coverage-in-readme.yml — 'Check the output coverage' step interpolates multiple `${{ steps.coverageComment.outputs.* }}` expressions directly into echo commands.

(a) update-coverage-on-readme.yml — 'Update Readme with Coverage Html' step interpolates `${{ steps.coverageComment.outputs.content }}` directly into a `sed` command: `sed -i '...c\...\n\${{ steps.coverageComment.outputs.content }}\n...' ./README.md`. This is particularly dangerous as the content output could contain shell metacharacters that alter the sed command or inject arbitrary shell commands.

Locations:

- `.github/workflows/live-test.yml:26`
- `.github/workflows/test-branch.yml:26`
- `.github/workflows/update-coverage-in-readme.yml:46`
- `.github/workflows/update-coverage-on-readme.yml:30`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all 6 workflow files:

1. **unpinned-uses**: Pinned all action references to full 40-char SHAs:
   - actions/checkout@v6 → @d23441a48e516b6c34aea4fa41551a30e30af803
   - MishaKav/pytest-coverage-comment@main → @09c28b6767e12a048e0b23e821ed03133ee48a1a
   - schneegans/dynamic-badges-action@v1.7.0 → @e9a478b16159b4d31420099ba146cdc50f134483
   - actions/cache@v5 → @caa296126883cff596d87d8935842f9db880ef25
   - MishaKav/jest-coverage-comment@main → @642ef024cc554a34b7082cea12c7bf63575ae151
   - actions-js/push@master → @968f4695ca558093eadb24ad83cc5891f47e0cdc

2. **script-injection**: Moved all ${{ }} expressions from run: blocks to env: blocks in live-test.yml (10 outputs), test-branch.yml (3 outputs), update-coverage-in-readme.yml (13 outputs), and update-coverage-on-readme.yml (the dangerous sed command with content output). All shell scripts now reference plain environment variables instead of directly interpolating GitHub expressions.

