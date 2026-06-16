<!-- markdownlint-disable -->

# Hardening Report: wei18--local-composite-action/1.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **wei18--local-composite-action/1.1.1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In the 'Find action repository directory' step, the shell variable $CURRENT is derived from $ACTION_PATH, which is populated from the untrusted input `inputs.action_path`. The value is written directly to $GITHUB_OUTPUT via `echo "path=$CURRENT" >> $GITHUB_OUTPUT` without first sanitizing it with `printf '%s' "$CURRENT" | tr -d '\n\r'`. An attacker-controlled `action_path` value containing embedded newlines could inject arbitrary key=value pairs into $GITHUB_OUTPUT, potentially overwriting subsequent step outputs.

Locations:

- `action.yml:68`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed the github-env-injection finding in action.yml at line 68. In the 'Find action repository directory' step, the $CURRENT value (derived from the untrusted input `inputs.action_path`) is now sanitized with `printf '%s' "$CURRENT" | tr -d '\n\r'` before being written to $GITHUB_OUTPUT. This prevents embedded newlines in the input from injecting arbitrary key=value pairs into $GITHUB_OUTPUT.

