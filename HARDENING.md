<!-- markdownlint-disable -->

# Hardening Report: cloudposse--github-action-matrix-outputs-read/1.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cloudposse--github-action-matrix-outputs-read/1.0.0** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The composite action references actions by mutable tag instead of a full commit SHA: 'uses: dcarbone/install-jq-action@v2.1.0' (line 18) and 'uses: actions/download-artifact@v4' (line 23).

Locations:

- `action.yml:18`
- `action.yml:23`

### script-injection (severity: high)

Sub-rule (a): an expression is placed directly in the run: script: 'result="$(find . -name ${{ inputs.matrix-step-name }} -maxdepth 2 -exec cat {} \; | jq ...)"'. The input is also unquoted inside the command substitution, so shell metacharacters in it would be executed. Fix: move the input to an env: variable and expand it as "$MATRIX_STEP_NAME".

Locations:

- `action.yml:28`

### github-env-injection (severity: high)

'echo "result=${result}" >> $GITHUB_OUTPUT' writes a value to $GITHUB_OUTPUT without sanitizing it first. The value comes from the contents of downloaded artifacts, and inputs.matrix-step-name picks which files are read. No printf '%s' ... | tr -d '\n\r' step runs before the write, and $GITHUB_OUTPUT is not quoted.

Locations:

- `action.yml:29`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.matrix-step-name }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:29`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, github-env-injection, static-inline-injection

**Notes:**

Pinned dcarbone/install-jq-action (v2.1.0) and actions/download-artifact (v4) to SHAs; moved inputs.matrix-step-name to env MATRIX_STEP_NAME and quoted it in find; captured jq output first (preserving errexit/pipefail) then stripped CR/LF before writing to quoted $GITHUB_OUTPUT (jq -c output is single-line, so no behavior change).

