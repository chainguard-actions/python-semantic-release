<!-- markdownlint-disable -->

# Hardening Report: python-semantic-release--python-semantic-release/v10.5.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **python-semantic-release--python-semantic-release/v10.5.1** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): The 'Evaluate | Detect if any of the combinations of file sets have changed' step interpolates ${{ steps.core-changed-files.outputs.build_any_changed }}, ${{ steps.ci-changed-files.outputs.ci_any_changed }}, and other steps.*.outputs.* expressions directly inside a run: shell command. These are untrusted workflow-controllable values that flow through YAML template substitution before the shell sees them, enabling script injection.

Locations:

- `.github/workflows/ci.yml:61`
- `.github/workflows/cicd.yml:48`

### script-injection (severity: high)

Rule (a): The 'Setup | Force release branch to be at workflow sha' step interpolates ${{ github.sha }} directly inside a run: shell command: `git reset --hard ${{ github.sha }}`. The github.* context is substituted by the YAML template engine before the shell executes the command.

Locations:

- `.github/workflows/cicd.yml:128`

### script-injection (severity: high)

Rule (a): The 'Setup | Force correct release branch on workflow sha' step interpolates ${{ github.ref_name }} directly inside a run: shell command: `git checkout -B ${{ github.ref_name }}`. The github.* context is substituted by the YAML template engine before the shell executes the command.

Locations:

- `.github/workflows/validate.yml:100`

### script-injection (severity: high)

Rule (a): The 'Build | Annotate next version' step interpolates ${{ steps.version.outputs.tag }} directly inside a run: shell command: `printf '%s\n' "::notice::Next release will be '${{ steps.version.outputs.tag }}'"`. The steps.*.outputs.* context is substituted by the YAML template engine before the shell executes the command.

Locations:

- `.github/workflows/validate.yml:129`

### script-injection (severity: high)

Rule (a): The 'Test | Run pytest -m e2e --comprehensive' step (Linux) interpolates ${{ steps.install.outputs.PKG_INSTALLED_DIR }} directly inside a run: shell command: `--cov=${{ steps.install.outputs.PKG_INSTALLED_DIR }}`. The steps.*.outputs.* context is substituted by the YAML template engine before the shell executes the command.

Locations:

- `.github/workflows/validate.yml:260`

### script-injection (severity: high)

Rule (a): The 'Test | Run pytest -m e2e' step (Windows, PowerShell) interpolates ${{ steps.install.outputs.PKG_INSTALLED_DIR }} directly inside a run: shell command: `` `--cov=${{ steps.install.outputs.PKG_INSTALLED_DIR }} ``. The steps.*.outputs.* context is substituted by the YAML template engine before the shell executes the command.

Locations:

- `.github/workflows/validate.yml:340`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed all 6 script injection findings across 3 workflow files:

1. **ci.yml** (line 61): Moved 7 `steps.*.outputs.*` expressions (`build_any_changed`, `ci_any_changed`, `docs_any_changed`, `src_any_changed`, `tests_any_changed`, `gha_src_any_changed`, `gha_tests_any_changed`) into the step's `env:` block and replaced inline `${{ }}` references with plain `$VAR_NAME` shell variables.

2. **cicd.yml** (line 48): Applied the same fix as ci.yml for the identical `eval-changes` step.

3. **cicd.yml** (line 128): Moved `${{ github.sha }}` to `env: WORKFLOW_SHA` and used `"$WORKFLOW_SHA"` in the `git reset --hard` command.

4. **validate.yml** (line 100): Moved `${{ github.ref_name }}` to `env: REF_NAME` and used `"$REF_NAME"` in the `git checkout -B` command.

5. **validate.yml** (line 129): Moved `${{ steps.version.outputs.tag }}` to `env: NEXT_TAG` and used `'$NEXT_TAG'` in the `printf` command.

6. **validate.yml** (line 260, Linux): Moved `${{ steps.install.outputs.PKG_INSTALLED_DIR }}` to `env: PKG_INSTALLED_DIR` and used `"$PKG_INSTALLED_DIR"` in the pytest `--cov` argument.

7. **validate.yml** (line 340, Windows/PowerShell): Moved `${{ steps.install.outputs.PKG_INSTALLED_DIR }}` to `env: PKG_INSTALLED_DIR` and used `"--cov=$env:PKG_INSTALLED_DIR"` in the PowerShell pytest command.

