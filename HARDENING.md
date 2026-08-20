<!-- markdownlint-disable -->

# Hardening Report: python-semantic-release--python-semantic-release/v10.4.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **python-semantic-release--python-semantic-release/v10.4.1** was hardened automatically. 0 finding(s) were identified and resolved across 2 iteration(s).

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed all 7 findings across 3 workflow files:

1. ci.yml (line 61): Moved 7 `${{ steps.*.outputs.* }}` expressions into `env:` block; replaced with plain shell vars in `run:` block.

2. cicd.yml (line 48): Same pattern - moved 7 step output expressions into `env:` block.

3. cicd.yml (line 125): Moved `${{ github.sha }}` into `env: WORKFLOW_SHA:` and used `"$WORKFLOW_SHA"` in `git reset --hard`.

4. validate.yml (line 100): Moved `${{ github.ref_name }}` into `env: REF_NAME:` and used `"$REF_NAME"` in `git checkout -B`.

5. validate.yml (lines 230, 330): Moved `${{ steps.install.outputs.PKG_INSTALLED_DIR }}` into `env: PKG_INSTALLED_DIR:` for both Linux (bash: `"$PKG_INSTALLED_DIR"`) and Windows (PowerShell: `"$env:PKG_INSTALLED_DIR"`) E2E test steps.

6. cicd.yml (line 240): Pinned `pypa/gh-action-pypi-publish@v1.13.0` → `@ed0c53931b1dc9bd32cbe73a98c7f6766f8a527e  # v1.13.0`.

7. validate.yml (5 occurrences): Pinned all `actions/upload-artifact@v4` → `@ea165f8d65b6e75b540449e92b4886f43607fa02  # v4`.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed script injection in `.github/workflows/validate.yml` at the 'Build | Annotate next version' step (line 130). Moved `${{ steps.version.outputs.tag }}` out of the `run:` shell command into an `env:` block as `NEXT_TAG`, and updated the shell command to reference `$NEXT_TAG` instead. This prevents the GitHub Actions template engine from substituting the value directly into the shell string before execution.

