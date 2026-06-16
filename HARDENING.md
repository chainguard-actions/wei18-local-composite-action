<!-- markdownlint-disable -->

# Hardening Report: wei18--local-composite-action/1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **wei18--local-composite-action/1.1.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In the 'Find action repository directory' step, the variable $CURRENT is derived from $ACTION_PATH, which is set from the untrusted input `inputs.action_path`. The line `echo "path=$CURRENT" >> $GITHUB_OUTPUT` writes this untrusted-derived value to $GITHUB_OUTPUT without the required sanitization step (`printf '%s' "$CURRENT" | tr -d '\n\r'`). An attacker-controlled value for `inputs.action_path` could inject newlines to smuggle arbitrary key=value pairs into GITHUB_OUTPUT.

Locations:

- `action.yml:57`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed the github-env-injection finding in action.yml at line 57. The $CURRENT variable (derived from untrusted input `inputs.action_path`) was being written directly to $GITHUB_OUTPUT without sanitization. The fix introduces a `safe_current` variable that strips newlines and carriage returns using `printf '%s' "$CURRENT" | tr -d '\n\r'` before writing to $GITHUB_OUTPUT, preventing newline injection attacks that could smuggle arbitrary key=value pairs into GITHUB_OUTPUT.

