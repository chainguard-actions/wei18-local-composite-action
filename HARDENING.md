<!-- markdownlint-disable -->

# Hardening Report: wei18--local-composite-action/1.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **wei18--local-composite-action/1.2.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In the 'Find action repository directory' step, two writes to $GITHUB_OUTPUT use values derived from untrusted inputs without the required sanitization (`printf '%s' ... | tr -d '\n\r'`). At line 79, `echo "path=$CURRENT" >> "$GITHUB_OUTPUT"` writes `$CURRENT`, which is initialized from `$ACTION_PATH` (holding `inputs.action_path || github.action_path`) and traversed via `dirname`. At line 100, `echo "path=$REF_DIR" >> "$GITHUB_OUTPUT"` writes `$REF_DIR`, also derived from `$ACTION_PATH`. A caller supplying a path containing embedded newlines could inject additional key=value pairs into GITHUB_OUTPUT, potentially overwriting outputs consumed by downstream steps.

Locations:

- `action.yml:79`
- `action.yml:100`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed two instances of GITHUB_OUTPUT injection in action.yml. At the first write (path=$CURRENT), added `safe_current=$(printf '%s' "$CURRENT" | tr -d '\n\r')` and changed the echo to use `$safe_current`. At the second write (path=$REF_DIR), added `safe_ref_dir=$(printf '%s' "$REF_DIR" | tr -d '\n\r')` and changed the echo to use `$safe_ref_dir`. Both values are derived from the untrusted `inputs.action_path` input and could have allowed newline injection into GITHUB_OUTPUT.

