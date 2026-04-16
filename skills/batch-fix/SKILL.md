---
name: batch-fix
description: Batch fix formatting issues across all Python files in the repo
---

Trigger when: user wants to fix Python formatting across the repo, run a lint sweep, or clean up formatting before a PR.

## Steps

### 0. Pre-flight: verify tools are installed
- Run `ruff --version` and `black --version` in Bash.
- If either is missing, install it: `pip install ruff black`.
- Confirm both are available before proceeding.

### 1. Discover all Python files
- Use Glob with pattern `**/*.py` from the repo root to collect all `.py` files.
- Exclude virtual environments and cache directories (`.venv`, `__pycache__`, `.tox`, `dist`, `build`).

### 2. Run ruff auto-fix
- Run `ruff check --fix <file>` on each Python file.
- Capture stdout/stderr and note which files had issues found and fixed.

### 3. Run black formatting
- Run `black <file>` on each Python file.
- Capture whether each file was reformatted or left unchanged.

### 4. Report results
Produce a summary table in this format:

```
File                  | Issues Found | Issues Fixed | Status
----------------------|--------------|--------------|--------
src/foo.py            | 3            | 3            | OK
src/bar.py            | 1            | 0            | FLAGGED
tests/test_baz.py     | 0            | 0            | Clean
```

- **OK**: all issues were auto-fixed.
- **Clean**: no issues found.
- **FLAGGED**: one or more issues could not be fixed automatically.

### 5. Flag unresolved files
For any file with status FLAGGED, output:
```
FLAGGED: src/bar.py
  Error: <exact ruff or black error message>
  Action needed: manual fix required
```

### 6. Confirmation pass
- Re-run `ruff check` (without `--fix`) across all files to confirm zero remaining violations.
- Re-run `black --check` across all files to confirm no further reformatting is needed.
- Report the final violation count. If non-zero, list the remaining issues explicitly.

Run with: /batch-fix
