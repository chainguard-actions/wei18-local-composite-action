<!-- markdownlint-disable -->

# Hardening Report: wei18--local-composite-action/v1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **wei18--local-composite-action/v1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

The 'Find action repository directory' step (id: action_repository) writes values derived from untrusted input to $GITHUB_OUTPUT without sanitizing them first. CURRENT is set from ACTION_PATH (${{ inputs.action_path || github.action_path }}) and changed with dirname, then written by `echo "path=$CURRENT" >> "$GITHUB_OUTPUT"`. REF_DIR comes from a glob under a directory derived from ACTION_PATH and is written by `echo "path=$REF_DIR" >> "$GITHUB_OUTPUT"`. Neither write is preceded by `printf '%s' ... | tr -d '\n\r'`. A value containing a newline could inject extra step outputs. Fix: `safe=$(printf '%s' "$CURRENT" | tr -d '\n\r'); echo "path=$safe" >> "$GITHUB_OUTPUT"`, and do the same for REF_DIR.

Locations:

- `action.yml:79`
- `action.yml:100`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

In action.yml, the 'Find action repository directory' step now removes newline and carriage-return characters from CURRENT and REF_DIR before writing them to $GITHUB_OUTPUT. It does this with printf '%s' | tr -d '\n\r'. Both values come straight from path variables and not from a command substitution that could fail, so this change doesn't alter how the step handles errors. Nothing else in the file was changed. I haven't run the action or any tests after the edit.

