<!-- markdownlint-disable -->

# Hardening Report: wei18--local-composite-action/1.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **wei18--local-composite-action/1.0.1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A GitHub Actions expression `${{ github.action_path }}` is directly interpolated inside a `run:` shell command string on line 25 of action.yml. The offending line is: `bash ${{ github.action_path }}/resolve-action-symlink.bash`. YAML template substitution injects this value into the shell command before the shell executes it, and the value is unquoted, meaning any special characters in the path could be interpreted by the shell. The safe fix is to use the pre-set `$GITHUB_ACTION_PATH` environment variable instead: `bash "$GITHUB_ACTION_PATH"/resolve-action-symlink.bash`.

Locations:

- `action.yml:25`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed script injection in action.yml line 25: replaced `bash ${{ github.action_path }}/resolve-action-symlink.bash` with `bash "$GITHUB_ACTION_PATH"/resolve-action-symlink.bash`. The $GITHUB_ACTION_PATH environment variable is automatically set by GitHub Actions and is equivalent to github.action_path, but using it avoids YAML template substitution injecting the value directly into the shell command string. The variable is now properly double-quoted to handle paths with spaces or special characters.

