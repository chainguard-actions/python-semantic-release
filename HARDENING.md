<!-- markdownlint-disable -->

# Hardening Report: python-semantic-release--python-semantic-release/v10.5.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **python-semantic-release--python-semantic-release/v10.5.3** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): ${{ }} expressions are interpolated directly inside run: shell command strings. In the 'eval-changes' job's 'all-changes' step, step outputs such as ${{ steps.core-changed-files.outputs.build_any_changed }}, ${{ steps.ci-changed-files.outputs.ci_any_changed }}, etc. are directly embedded in a bash if-statement. These values flow through YAML template substitution before the shell sees them, enabling script injection if any step output contains shell metacharacters.

Locations:

- `.github/workflows/ci.yml:64`
- `.github/workflows/cicd.yml:48`

### script-injection (severity: high)

Rule (a): ${{ github.sha }} is interpolated directly inside a run: shell command string: `git reset --hard ${{ github.sha }}`. Although github.sha is typically a hex string, any ${{ }} expression directly in a run: block is a script-injection finding per the check rules.

Locations:

- `.github/workflows/cicd.yml:125`

### script-injection (severity: high)

Rule (a): Multiple ${{ }} expressions are interpolated directly inside run: shell command strings in validate.yml: (1) `git checkout -B ${{ github.ref_name }}` in the build job's 'Force correct release branch on workflow sha' step — github.ref_name is attacker-controllable via branch names; (2) `printf '%s\n' "::notice::Next release will be '${{ steps.version.outputs.tag }}'"` in the 'Annotate next version' step — steps.*.outputs.* is listed as an untrusted source; (3) `--cov=${{ steps.install.outputs.PKG_INSTALLED_DIR }}` in the test-linux 'Run pytest -m e2e --comprehensive' step; (4) `--cov=${{ steps.install.outputs.PKG_INSTALLED_DIR }}` in the test-windows 'Run pytest -m e2e' step.

Locations:

- `.github/workflows/validate.yml:100`
- `.github/workflows/validate.yml:129`
- `.github/workflows/validate.yml:261`
- `.github/workflows/validate.yml:383`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed all script injection findings across 3 workflow files:
1. ci.yml (line 64): Moved 7 step outputs (build_any_changed, ci_any_changed, docs_any_changed, src_any_changed, tests_any_changed, gha_src_any_changed, gha_tests_any_changed) from ${{ }} expressions in the bash if-statement into the step's env: block.
2. cicd.yml (line 48): Same fix as ci.yml for the identical all-changes step.
3. cicd.yml (line 125): Moved ${{ github.sha }} into env: WORKFLOW_SHA and referenced as "$WORKFLOW_SHA" in the git reset command.
4. validate.yml (line 100): Moved ${{ github.ref_name }} into env: REF_NAME and referenced as "$REF_NAME" in git checkout -B.
5. validate.yml (line 129): Moved ${{ steps.version.outputs.tag }} into env: RELEASE_TAG and referenced as $RELEASE_TAG in printf.
6. validate.yml (line 261): Moved ${{ steps.install.outputs.PKG_INSTALLED_DIR }} into env: PKG_INSTALLED_DIR and referenced as "$PKG_INSTALLED_DIR" in the pytest --cov argument (Linux).
7. validate.yml (line 383): Moved ${{ steps.install.outputs.PKG_INSTALLED_DIR }} into env: PKG_INSTALLED_DIR and referenced as "$env:PKG_INSTALLED_DIR" in the pytest --cov argument (Windows PowerShell).

