<!-- markdownlint-disable -->

# Hardening Report: wei18--local-composite-action/1.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **wei18--local-composite-action/1.0.1** was hardened automatically. 3 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a) violation: The `run:` block in action.yml directly interpolates `${{ github.action_path }}` inside a shell command string: `bash ${{ github.action_path }}/resolve-action-symlink.bash`. Any `${{ ... }}` expression interpolated directly into a `run:` block is a script-injection risk because the value is substituted into the shell command before the shell parses it, allowing an attacker-controlled or unexpected value to alter the command. The safe alternative is to pass the value via an `env:` variable and reference it as a quoted shell variable, e.g. `env: ACTION_PATH: ${{ github.action_path }}` then `bash "$ACTION_PATH/resolve-action-symlink.bash"`.

Locations:

- `action.yml:25`

### unpinned-uses (severity: high)

The workflow file .github/workflows/test.yml references `wei18/local-composite-action/.github/composite-actions/example@main` — a mutable branch ref (`@main`) rather than a pinned 40-character commit SHA. A branch ref can be silently updated to point to different (potentially malicious) code at any time, creating a supply-chain risk. Pin the reference to a full SHA, e.g. `wei18/local-composite-action/.github/composite-actions/example@<40-char-sha> # main`.

Locations:

- `.github/workflows/test.yml:12`

### missing-permissions (severity: medium)

The workflow file .github/workflows/test.yml has no top-level `permissions:` key and the single job `test` also has no `permissions:` key. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (e.g. `write` access to contents). A minimal explicit `permissions:` block (e.g. `permissions: {}` or only the scopes actually needed) should be added at the top level or on every job.

Locations:

- `.github/workflows/test.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

1. action.yml (line 25): Fixed script injection by adding `ACTION_PATH: ${{ github.action_path }}` to the step's env block and replacing `bash ${{ github.action_path }}/resolve-action-symlink.bash` with `bash "$ACTION_PATH/resolve-action-symlink.bash"`. 2. .github/workflows/test.yml (line 12): Pinned `wei18/local-composite-action/.github/composite-actions/example@main` to full SHA `3e6f017c4bf7f9451fc0b6d987d47507fa401aa3` with `# main` comment. 3. .github/workflows/test.yml (line 1): Added `permissions: {}` at the top level to explicitly restrict token permissions.

### Iteration 2

**Fixes applied:** unpinned-uses

**Notes:**

Pinned `wei18/local-composite-action@main` to `wei18/local-composite-action@3e6f017c4bf7f9451fc0b6d987d47507fa401aa3 # main` in `.github/composite-actions/example/action.yml` line 6. The SHA was resolved via git ls-remote against the upstream repository.

