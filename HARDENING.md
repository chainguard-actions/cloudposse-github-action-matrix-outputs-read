<!-- markdownlint-disable -->

# Hardening Report: cloudposse--github-action-matrix-outputs-read/0.1.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cloudposse--github-action-matrix-outputs-read/0.1.2** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): the input expression is placed directly inside the run script of the 'context' step: `result="$(find . -name ${{ inputs.matrix-step-name }} -maxdepth 2 -exec cat {} \; | jq ...)"`. The value is substituted unquoted before the shell runs, so a crafted matrix-step-name could run arbitrary commands. Fix: pass it in through an env var and double-quote that variable in the script.

Locations:

- `action.yml:29`

### github-env-injection (severity: high)

The 'context' step writes `echo "result=${result}" >> $GITHUB_OUTPUT`. `result` comes from files found using the untrusted input inputs.matrix-step-name, and the write is not preceded by `printf '%s' ... | tr -d '\n\r'`, so the value is written to GITHUB_OUTPUT unsanitized.

Locations:

- `action.yml:30`

### unpinned-uses (severity: high)

Two composite steps reference actions by tag instead of a full 40-character commit SHA: `uses: dcarbone/install-jq-action@v2.1.0` and `uses: actions/download-artifact@v3`.

Locations:

- `action.yml:19`
- `action.yml:24`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.matrix-step-name }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:29`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, github-env-injection, unpinned-uses

**Notes:**

Moved inputs.matrix-step-name to env MATRIX_STEP_NAME, used double-quoted in find; result is passed through printf | tr -d '\n\r' before being written to quoted "$GITHUB_OUTPUT" (jq -c output is single-line, so behaviour is unchanged); pinned dcarbone/install-jq-action to 8867ddb4 (v2.1.0) and actions/download-artifact to 9bc31d5c (v3).

