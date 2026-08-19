<!-- markdownlint-disable -->

# Hardening Report: wei18--local-composite-action/1.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **wei18--local-composite-action/1.1.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple files reference actions using the mutable branch ref '@main' instead of a pinned 40-character commit SHA. This exposes the workflow to supply-chain attacks if the referenced branch is compromised. Failing references:
- .github/workflows/test.yml: `uses: wei18/local-composite-action/.github/composite-actions/example@main`
- .github/composite-actions/example/action.yml: `uses: wei18/local-composite-action@main`
- .github/composite-actions/example/just-composite-action/action.yml: `uses: wei18/local-composite-action@main`

Locations:

- `.github/workflows/test.yml:12`
- `.github/composite-actions/example/action.yml:5`
- `.github/composite-actions/example/just-composite-action/action.yml:5`

### missing-permissions (severity: medium)

The workflow file .github/workflows/test.yml has no top-level `permissions:` key and the single job 'test' also has no `permissions:` key. Without explicit permissions, the job inherits the default repository token permissions, which may be overly broad (write access to contents and other scopes depending on repository settings).

Locations:

- `.github/workflows/test.yml:1`

### github-env-injection (severity: high)

In the 'Find action repository directory' step of action.yml, the shell variable $CURRENT (derived from $ACTION_PATH, which is set from the untrusted input `inputs.action_path`) is written directly to $GITHUB_OUTPUT without the required sanitization step (`printf '%s' "$CURRENT" | tr -d '\n\r'`). An attacker-controlled value containing newlines could inject arbitrary key=value pairs into GITHUB_OUTPUT, potentially poisoning subsequent steps. The offending line is: `echo "path=$CURRENT" >> $GITHUB_OUTPUT`

Locations:

- `action.yml:57`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, github-env-injection

**Notes:**

1. unpinned-uses: Pinned all three `wei18/local-composite-action@main` references to SHA `3e6f017c4bf7f9451fc0b6d987d47507fa401aa3` in .github/workflows/test.yml, .github/composite-actions/example/action.yml, and .github/composite-actions/example/just-composite-action/action.yml. 2. missing-permissions: Added `permissions: {}` at the top level of .github/workflows/test.yml. 3. github-env-injection: In action.yml line 57, sanitized the `$CURRENT` value before writing to $GITHUB_OUTPUT using `printf '%s' "$CURRENT" | tr -d '\n\r'` to strip any embedded newlines that could inject additional key=value pairs.

