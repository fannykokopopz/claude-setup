---
name: format-sweep
description: Autonomous formatting audit and auto-fix sweep. Reads CLAUDE.md rules, generates a Python linter, fixes all violations, and produces a FORMATTING_REPORT.md. Works across any repo.
---

Trigger when: user asks to run a formatting sweep, lint all files, or auto-fix formatting violations across the repo.

Run a complete formatting audit and auto-fix sweep autonomously. Do not stop to ask about ambiguous cases — apply the closest matching rule from CLAUDE.md and note your interpretation in the report.

## Steps

### 1 — Extract formatting rules
- Read `CLAUDE.md` (check repo root, `.claude/`, and `~/.claude/` in that order)
- Extract all formatting rules: heading styles, bold patterns, color highlights, section naming conventions
- If no CLAUDE.md exists, use sensible defaults: H2 for sections, bold lead phrases in bullets, no color highlights

### 2 — Generate linter
- Create `scripts/lint_formatting.py` that:
  - Accepts one or more file paths as arguments
  - Checks each file against the extracted rules
  - Reports each violation with: file path, line number, rule violated, suggested fix
  - Exits with code 0 if no violations, code 1 if violations found

### 3 — Run linter
- Run: `python scripts/lint_formatting.py **/*.md`
- Collect all violations

### 4 — Auto-fix
- For each violation, fix it directly in the source file
- Apply the closest matching rule and note any interpretation assumptions

### 5 — Re-run and iterate
- Re-run the linter after fixes
- If new violations appear, fix and re-run (max 5 iterations)
- Stop early if zero violations are confirmed

### 6 — Generate report
- Write `FORMATTING_REPORT.md` containing:
  - Total violations found
  - Violations by rule type
  - All fixes applied (file, line, what changed)
  - Any assumptions made for ambiguous cases

### 7 — Commit
- Stage: `scripts/lint_formatting.py`, all fixed files, `FORMATTING_REPORT.md`
- Commit message: `style: automated formatting sweep - <N> fixes across <M> files`
- Push to remote if available

Run with: /format-sweep
