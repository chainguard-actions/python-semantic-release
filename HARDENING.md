<!-- markdownlint-disable -->

# Hardening Report: python-semantic-release--python-semantic-release/v10.5.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **python-semantic-release--python-semantic-release/v10.5.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): ${{ steps.core-changed-files.outputs.build_any_changed }}, ${{ steps.ci-changed-files.outputs.ci_any_changed }}, and other steps.*.outputs.* expressions are interpolated directly inside a run: shell command string. These values flow through YAML template substitution before the shell sees them, enabling script injection. The offending lines are the if [ "${{ steps.*.outputs.* }}" == "true" ] comparisons in the 'Evaluate | Detect if any of the combinations of file sets have changed' step.

Locations:

- `.github/workflows/ci.yml:61`

### script-injection (severity: high)

Rule (a): ${{ steps.core-changed-files.outputs.build_any_changed }}, ${{ steps.ci-changed-files.outputs.ci_any_changed }}, and other steps.*.outputs.* expressions are interpolated directly inside a run: shell command string in the eval-changes job. Additionally, ${{ github.sha }} is interpolated directly in a run: block: 'git reset --hard ${{ github.sha }}'. Both are script-injection risks as ${{ }} expressions are substituted before the shell parses the command.

Locations:

- `.github/workflows/cicd.yml:48`
- `.github/workflows/cicd.yml:107`

### script-injection (severity: high)

Rule (a): Multiple ${{ }} expressions are interpolated directly inside run: shell command strings in validate.yml: (1) 'git checkout -B ${{ github.ref_name }}' in the build job's 'Setup | Force correct release branch on workflow sha' step; (2) "::notice::Next release will be '${{ steps.version.outputs.tag }}'" in the build job's 'Build | Annotate next version' step; (3) '--cov=${{ steps.install.outputs.PKG_INSTALLED_DIR }}' in the test-linux job's pytest run step; (4) '--cov=${{ steps.install.outputs.PKG_INSTALLED_DIR }}' in the test-windows job's pytest run step. All of these substitute ${{ }} expressions directly into shell commands before the shell parses them.

Locations:

- `.github/workflows/validate.yml:97`
- `.github/workflows/validate.yml:118`
- `.github/workflows/validate.yml:230`
- `.github/workflows/validate.yml:330`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed all script injection vulnerabilities across three workflow files:

1. **ci.yml** (line 61): Moved 7 `${{ steps.*.outputs.* }}` expressions from the shell comparison in the 'Evaluate | Detect if any of the combinations of file sets have changed' step into an `env:` block (BUILD_ANY_CHANGED, CI_ANY_CHANGED, DOCS_ANY_CHANGED, SRC_ANY_CHANGED, TESTS_ANY_CHANGED, GHA_SRC_ANY_CHANGED, GHA_TESTS_ANY_CHANGED). Shell now references plain env vars.

2. **cicd.yml** (line 48): Same fix as ci.yml for the eval-changes job's detection step — 7 `${{ steps.*.outputs.* }}` expressions moved to `env:` block.

3. **cicd.yml** (line 107): Fixed `git reset --hard ${{ github.sha }}` in the release job by moving `github.sha` into an `env:` block as `WORKFLOW_SHA` and using `"$WORKFLOW_SHA"` in the shell command.

4. **validate.yml** (line 97): Fixed `git checkout -B ${{ github.ref_name }}` by moving `github.ref_name` into an `env:` block as `REF_NAME` and using `"$REF_NAME"` in the shell command.

5. **validate.yml** (line 118): Fixed `printf '%s\n' "::notice::Next release will be '${{ steps.version.outputs.tag }}'"` by moving `steps.version.outputs.tag` into an `env:` block as `NEXT_RELEASE_TAG` and referencing `$NEXT_RELEASE_TAG` in the shell command.

6. **validate.yml** (line 230): Fixed `--cov=${{ steps.install.outputs.PKG_INSTALLED_DIR }}` in the test-linux pytest run by moving the expression into an `env:` block as `PKG_INSTALLED_DIR` and using `--cov="$PKG_INSTALLED_DIR"` in the shell command.

7. **validate.yml** (line 330): Fixed `--cov=${{ steps.install.outputs.PKG_INSTALLED_DIR }}` in the test-windows PowerShell pytest run by adding an `env:` block with `PKG_INSTALLED_DIR` and using `"--cov=$env:PKG_INSTALLED_DIR"` in the PowerShell command.

