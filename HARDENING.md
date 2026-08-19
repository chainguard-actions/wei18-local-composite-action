<!-- markdownlint-disable -->

# Hardening Report: wei18--local-composite-action/1.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **wei18--local-composite-action/1.2.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple `uses:` references use mutable tags instead of pinned 40-character SHA commit hashes, making the action vulnerable to supply-chain attacks if the referenced tag is moved or overwritten.

Failing references:
- `.github/workflows/test.yml`: `actions/checkout@v4` (tag, not SHA)
- `.github/workflows/test.yml`: `wei18/local-composite-action/.github/composite-actions/example@main` (branch, not SHA)
- `.github/composite-actions/example/action.yml`: `wei18/local-composite-action@main` (branch, not SHA)
- `.github/composite-actions/example/just-composite-action/action.yml`: `wei18/local-composite-action@main` (branch, not SHA)

Locations:

- `.github/workflows/test.yml:13`
- `.github/workflows/test.yml:19`
- `.github/composite-actions/example/action.yml:5`
- `.github/composite-actions/example/just-composite-action/action.yml:5`

### permissions (severity: medium)

`.github/workflows/test.yml` has no top-level `permissions:` key, and neither of its jobs (`unit`, `test`) defines a job-level `permissions:` block. This means the workflow runs with the default (broad) GitHub token permissions, violating the principle of least privilege.

Locations:

- `.github/workflows/test.yml:1`

### github-env-injection (severity: high)

In `action.yml`, the 'Find action repository directory' step writes the variables `$CURRENT` and `$REF_DIR` to `$GITHUB_OUTPUT` without sanitization. Both variables are derived from `$ACTION_PATH`, which is sourced from the untrusted input `inputs.action_path || github.action_path`. An attacker-controlled value containing newline characters could inject arbitrary key-value pairs into `$GITHUB_OUTPUT`, potentially influencing subsequent steps. The required sanitization (`printf '%s' "$VAR" | tr -d '\n\r'`) is absent before each write.

Failing lines:
- `echo "path=$CURRENT" >> "$GITHUB_OUTPUT"` (derived from untrusted `$ACTION_PATH`)
- `echo "path=$REF_DIR" >> "$GITHUB_OUTPUT"` (derived from untrusted `$ACTION_PATH`)

Locations:

- `action.yml:68`
- `action.yml:88`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, permissions, github-env-injection

**Notes:**

Fixed all three findings: (1) Pinned all four mutable uses references to full 40-char SHAs with tag comments preserved (actions/checkout@v4→SHA, wei18/local-composite-action@main→SHA in three files); (2) Added `permissions: {}` top-level block to .github/workflows/test.yml; (3) Sanitized both $GITHUB_OUTPUT writes in action.yml by piping through `printf '%s' | tr -d '\n\r'` before writing $CURRENT and $REF_DIR values.

