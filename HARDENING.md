<!-- markdownlint-disable -->

# Hardening Report: cloudposse--github-action-matrix-outputs-read/0.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cloudposse--github-action-matrix-outputs-read/0.1.1** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two composite steps use version tags instead of full commit SHAs: 'uses: dcarbone/install-jq-action@v1.0.0' and 'uses: actions/download-artifact@v3'.

Locations:

- `action.yml:19`
- `action.yml:24`

### script-injection (severity: high)

Sub-rule (a): an expression is placed directly in the run script, unquoted: 'result="$(find . -name ${{ inputs.matrix-step-name }} -maxdepth 2 -exec cat {} \; | jq ...)"'. A crafted input value can inject shell commands. Fix: pass the input through an env: variable and quote it ("$MATRIX_STEP_NAME").

Locations:

- `action.yml:28`

### github-env-injection (severity: high)

'echo "result=${result}" >> $GITHUB_OUTPUT' writes an untrusted value to GITHUB_OUTPUT. The value comes from inputs.matrix-step-name and from the contents of downloaded artifact files. Nothing strips newlines first (printf '%s' ... | tr -d '\n\r'), so extra output lines can be injected.

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

Pinned dcarbone/install-jq-action (v1.0.0) and actions/download-artifact (v3) to full SHAs. Moved inputs.matrix-step-name into env MATRIX_STEP_NAME and quoted it in the find -name argument (find still does the glob matching). Stripped newlines from the result before writing it to GITHUB_OUTPUT, and quoted $GITHUB_OUTPUT. jq -c already prints a single line, so the output does not change.

