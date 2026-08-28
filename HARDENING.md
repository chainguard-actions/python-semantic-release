<!-- markdownlint-disable -->

# Hardening Report: python-semantic-release--python-semantic-release/v10.6.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **python-semantic-release--python-semantic-release/v10.6.2** was hardened automatically. 8 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): Direct ${{ }} expression interpolation inside run: blocks. The step 'Evaluate | Detect if any of the combinations of file sets have changed' embeds steps.*.outputs.* values directly in shell conditionals: `if [ "${{ steps.core-changed-files.outputs.build_any_changed }}" == "true" ]` and six similar expressions. An attacker who can influence step outputs could inject shell metacharacters.

Locations:

- `.github/workflows/ci.yml:64`

### script-injection (severity: high)

Rule (a): Direct ${{ }} expression interpolation inside run: blocks. The step 'Evaluate | Detect if any of the combinations of file sets have changed' embeds steps.*.outputs.* values directly in shell conditionals: `if [ "${{ steps.core-changed-files.outputs.build_any_changed }}" == "true" ]` and six similar expressions.

Locations:

- `.github/workflows/cicd.yml:48`

### script-injection (severity: high)

Rule (a): Direct ${{ }} expression interpolation inside a run: block. The step 'Setup | Force release branch to be at workflow sha' runs: `git reset --hard ${{ github.sha }}`. Any ${{ }} expression directly in a run: block is a script-injection risk regardless of the context.

Locations:

- `.github/workflows/cicd.yml:107`

### script-injection (severity: high)

Rule (a): Direct ${{ }} expression interpolation inside a run: block. The step 'Setup | Force correct release branch on workflow sha' runs: `git checkout -B ${{ github.ref_name }}`. The github.ref_name value is interpolated directly into the shell command before the shell parses it.

Locations:

- `.github/workflows/validate.yml:100`

### script-injection (severity: high)

Rule (a): Direct ${{ }} expression interpolation inside a run: block. The step 'Build | Annotate next version' runs: `printf '%s\n' "::notice::Next release will be '${{ steps.version.outputs.tag }}'"`. The steps.version.outputs.tag value is interpolated directly into the shell command.

Locations:

- `.github/workflows/validate.yml:113`

### script-injection (severity: high)

Rule (a): Direct ${{ }} expression interpolation inside a run: block. The 'Test | Run pytest -m e2e --comprehensive' step (test-linux job) passes `--cov=${{ steps.install.outputs.PKG_INSTALLED_DIR }}` directly in the shell command. The steps.install.outputs.PKG_INSTALLED_DIR value is interpolated before the shell parses the command.

Locations:

- `.github/workflows/validate.yml:253`

### script-injection (severity: high)

Rule (a): Direct ${{ }} expression interpolation inside a run: block. The 'Setup | Harden Windows runner for git-heavy tests' step runs: `Add-MpPreference -ExclusionPath "${{ github.workspace }}"`. The github.workspace value is interpolated directly into the PowerShell command.

Locations:

- `.github/workflows/validate.yml:352`

### script-injection (severity: high)

Rule (a): Direct ${{ }} expression interpolation inside a run: block. The 'Test | Run pytest -m e2e' step (test-windows job) passes `` `--cov=${{ steps.install.outputs.PKG_INSTALLED_DIR }} `` directly in the PowerShell command. The steps.install.outputs.PKG_INSTALLED_DIR value is interpolated before the shell parses the command.

Locations:

- `.github/workflows/validate.yml:403`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed 8 script-injection findings across 3 workflow files:

1. **ci.yml (line 64)**: Moved 7 `steps.*.outputs.*` expressions (`build_any_changed`, `ci_any_changed`, `docs_any_changed`, `src_any_changed`, `tests_any_changed`, `gha_src_any_changed`, `gha_tests_any_changed`) from shell conditionals into the step's `env:` block, referencing them as `$BUILD_ANY_CHANGED`, `$CI_ANY_CHANGED`, etc.

2. **cicd.yml (line 48)**: Same fix as ci.yml — moved 7 `steps.*.outputs.*` expressions into `env:` block.

3. **cicd.yml (line 107)**: Moved `${{ github.sha }}` into `env: WORKFLOW_SHA` and used `"$WORKFLOW_SHA"` in the `git reset --hard` command.

4. **validate.yml (line 100)**: Moved `${{ github.ref_name }}` into `env: REF_NAME` and used `"$REF_NAME"` in the `git checkout -B` command.

5. **validate.yml (line 113)**: Moved `${{ steps.version.outputs.tag }}` into `env: NEXT_RELEASE_TAG` and used `$NEXT_RELEASE_TAG` in the `printf` command.

6. **validate.yml (line 253)**: Moved `${{ steps.install.outputs.PKG_INSTALLED_DIR }}` into `env: PKG_INSTALLED_DIR` and used `"$PKG_INSTALLED_DIR"` in the `--cov=` pytest argument (test-linux job).

7. **validate.yml (line 352)**: Moved `${{ github.workspace }}` into `env: GH_WORKSPACE` and used `"$env:GH_WORKSPACE"` in the PowerShell `Add-MpPreference` command.

8. **validate.yml (line 403)**: Moved `${{ steps.install.outputs.PKG_INSTALLED_DIR }}` into `env: PKG_INSTALLED_DIR` and used `"--cov=$env:PKG_INSTALLED_DIR"` in the PowerShell pytest command (test-windows job).

