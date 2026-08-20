<!-- markdownlint-disable -->

# Hardening Report: python-semantic-release--python-semantic-release/v10.5.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **python-semantic-release--python-semantic-release/v10.5.2** was hardened automatically. 7 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): ${{ }} expressions are interpolated directly inside run: shell commands. In ci.yml, the 'Evaluate | Detect if any of the combinations of file sets have changed' step uses ${{ steps.core-changed-files.outputs.build_any_changed }}, ${{ steps.ci-changed-files.outputs.ci_any_changed }}, and other steps.*.outputs.* expressions directly in shell if-conditions. Any of these step outputs could contain shell metacharacters injected by a malicious action.

Locations:

- `.github/workflows/ci.yml:61`

### script-injection (severity: high)

Sub-rule (a): ${{ }} expressions are interpolated directly inside run: shell commands. In cicd.yml, the 'Evaluate | Detect if any of the combinations of file sets have changed' step uses ${{ steps.core-changed-files.outputs.build_any_changed }}, ${{ steps.ci-changed-files.outputs.ci_any_changed }}, and other steps.*.outputs.* expressions directly in shell if-conditions.

Locations:

- `.github/workflows/cicd.yml:48`

### script-injection (severity: high)

Sub-rule (a): ${{ github.sha }} is interpolated directly inside a run: shell command: `git reset --hard ${{ github.sha }}`. Although github.sha is typically a hex SHA, any ${{ }} expression in a run: block is a script-injection risk as it flows through YAML template substitution before the shell processes it.

Locations:

- `.github/workflows/cicd.yml:148`

### script-injection (severity: high)

Sub-rule (a): ${{ github.ref_name }} is interpolated directly inside a run: shell command: `git checkout -B ${{ github.ref_name }}`. Branch names can contain shell metacharacters and this value flows through YAML template substitution before the shell processes it.

Locations:

- `.github/workflows/validate.yml:103`

### script-injection (severity: high)

Sub-rule (a): ${{ steps.version.outputs.tag }} is interpolated directly inside a run: shell command: `printf '%s\n' "::notice::Next release will be '${{ steps.version.outputs.tag }}'"`. Step outputs can contain shell metacharacters.

Locations:

- `.github/workflows/validate.yml:130`

### script-injection (severity: high)

Sub-rule (a): ${{ steps.install.outputs.PKG_INSTALLED_DIR }} is interpolated directly inside a run: shell command in the test-linux job: `--cov=${{ steps.install.outputs.PKG_INSTALLED_DIR }}`. Step outputs flow through YAML template substitution before the shell processes them.

Locations:

- `.github/workflows/validate.yml:175`

### script-injection (severity: high)

Sub-rule (a): ${{ steps.install.outputs.PKG_INSTALLED_DIR }} is interpolated directly inside a run: shell command in the test-windows job (PowerShell): `` `--cov=${{ steps.install.outputs.PKG_INSTALLED_DIR }} ``. Step outputs flow through YAML template substitution before the shell processes them.

Locations:

- `.github/workflows/validate.yml:280`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed all 7 script-injection findings across 3 workflow files:

1. ci.yml (line 61): Moved 7 steps.*.outputs.* expressions (${{ steps.core-changed-files.outputs.build_any_changed }}, ${{ steps.ci-changed-files.outputs.ci_any_changed }}, and 5 others) from shell if-conditions into an env: block. Shell now references $BUILD_ANY_CHANGED, $CI_ANY_CHANGED, etc.

2. cicd.yml (line 48): Same fix as ci.yml - moved 7 steps.*.outputs.* expressions from shell if-conditions into an env: block.

3. cicd.yml (line 148): Moved ${{ github.sha }} from `git reset --hard ${{ github.sha }}` into env: block as WORKFLOW_SHA. Shell now uses `git reset --hard "$WORKFLOW_SHA"`.

4. validate.yml (line 103): Moved ${{ github.ref_name }} from `git checkout -B ${{ github.ref_name }}` into env: block as REF_NAME. Shell now uses `git checkout -B "$REF_NAME"`.

5. validate.yml (line 130): Moved ${{ steps.version.outputs.tag }} from printf command into env: block as NEXT_RELEASE_TAG. Shell now uses `printf '%s\n' "::notice::Next release will be '$NEXT_RELEASE_TAG'"`.

6. validate.yml (line 175): Moved ${{ steps.install.outputs.PKG_INSTALLED_DIR }} from pytest --cov argument into env: block as PKG_INSTALLED_DIR. Shell now uses `--cov="$PKG_INSTALLED_DIR"`.

7. validate.yml (line 280): Moved ${{ steps.install.outputs.PKG_INSTALLED_DIR }} from PowerShell pytest --cov argument into env: block as PKG_INSTALLED_DIR. PowerShell now uses `"--cov=$env:PKG_INSTALLED_DIR"`.

