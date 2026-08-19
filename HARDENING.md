<!-- markdownlint-disable -->

# Hardening Report: wei18--local-composite-action/1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **wei18--local-composite-action/1.1.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In action.yml, the 'Find action repository directory' step (step 2) writes the value of `$CURRENT` to `$GITHUB_OUTPUT` via `echo "path=$CURRENT" >> $GITHUB_OUTPUT`. The variable `$CURRENT` is derived from `$ACTION_PATH`, which is sourced from `inputs.action_path` — an untrusted caller-controlled input. This write is not preceded by the required sanitization step (`printf '%s' "$CURRENT" | tr -d '\n\r'`), allowing a newline injection attack that could poison subsequent steps via GITHUB_OUTPUT.

Locations:

- `action.yml:57`

### unpinned-uses (severity: high)

Multiple files reference external actions using mutable branch refs (`@main`) instead of pinned 40-character commit SHAs, making the action vulnerable to supply-chain attacks if the referenced branch is compromised or force-pushed.
- `.github/workflows/test.yml`: `uses: wei18/local-composite-action/.github/composite-actions/example@main`
- `.github/composite-actions/example/action.yml`: `uses: wei18/local-composite-action@main`
- `.github/composite-actions/example/just-composite-action/action.yml`: `uses: wei18/local-composite-action@main`

Locations:

- `.github/workflows/test.yml:12`
- `.github/composite-actions/example/action.yml:6`
- `.github/composite-actions/example/just-composite-action/action.yml:6`

### missing-permissions (severity: medium)

The workflow file `.github/workflows/test.yml` has no top-level `permissions:` key, and the `test` job also has no job-level `permissions:` key. Without explicit permissions, the workflow runs with the default (potentially broad) token permissions, violating the principle of least privilege.

Locations:

- `.github/workflows/test.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection, unpinned-uses, missing-permissions

**Notes:**

1. Fixed github-env-injection in action.yml step 2: sanitized $CURRENT with `printf '%s' "$CURRENT" | tr -d '\n\r'` before writing to $GITHUB_OUTPUT. 2. Pinned all three unpinned @main references to SHA 3e6f017c4bf7f9451fc0b6d987d47507fa401aa3 (with # main comment) in .github/workflows/test.yml, .github/composite-actions/example/action.yml, and .github/composite-actions/example/just-composite-action/action.yml. 3. Added `permissions: {}` top-level block to .github/workflows/test.yml to enforce least privilege.

