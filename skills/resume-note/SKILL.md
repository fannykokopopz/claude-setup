---
name: resume-note
description: Create a clean session handoff note with current state, changes, blockers, and exact next step.
---

## Trigger conditions

Invoke this skill when:
- The work session is ending or winding down
- The user says "resume note", "session note", "handoff note", or "wrap up"
- The user wants to hand off work to a future session or another agent
- The user says "what did we do today" or "summarise this session"

---

## Step-by-step instructions

Produce the following six sections in order. Keep each section tight — 2–5 bullet points maximum unless complexity demands more. Be specific: name files, functions, commands, and errors by their actual names.

### 1. What was attempted
List every meaningful action taken this session, including failed or abandoned attempts. Do not omit failures.
- Start each bullet with a verb (e.g. "Investigated...", "Ran...", "Rewrote...")
- Include the file path or command name where relevant

### 2. What was completed
List only items that are fully done and verified — not in-progress.
- If nothing is complete, say so explicitly
- For code changes, name the file and function/section changed

### 3. What changed
List concrete file or config changes made this session.
- Format: `path/to/file` — one-line description of the change
- Include any environment variable, schema, or dependency changes
- If nothing changed on disk, say "No files modified"

### 4. What failed or remains uncertain
Be honest. List anything that:
- Did not work as expected (include the error or symptom)
- Was attempted but not validated
- Is a known unknown or risk going forward

### 5. Exact next step
Give a single, unambiguous next action. Not a list — one step.
- Start with a verb
- Name the file, command, or person involved
- State the expected outcome

### 6. Resume prompt (paste-ready)
Write a short paragraph the user can paste into a new session to restore full context. It should:
- State the repo/project name
- Summarise what was just done (1–2 sentences)
- State what is broken or pending (if anything)
- End with the exact next step

---

## Example output

```
## Session Note — 2026-04-15

### 1. What was attempted
- Investigated why `checker.py` was skipping generators with IDs above 1000
- Ran `python checker.py --dry-run` to reproduce the filter bug
- Rewrote the `apply_strict_id_gate()` function in `checker.py`
- Attempted to add unit tests — blocked by missing fixture data

### 2. What was completed
- `apply_strict_id_gate()` rewritten and manually verified against 3 sample cases
- `checker.py` indentation fixed (run() call was misaligned, causing silent skip)

### 3. What changed
- `checker.py` — rewrote `apply_strict_id_gate()`, fixed indentation on `run()` call
- No config or dependency changes

### 4. What failed or remains uncertain
- Unit tests not added — `tests/fixtures/` directory is empty, unclear what format is expected
- The "blocked" vs "not-found" colour bleed fix was applied but not tested across all terminal widths

### 5. Exact next step
Add at least one fixture file to `tests/fixtures/` and write a test for `apply_strict_id_gate()` to confirm the ID boundary logic holds for edge cases (IDs at exactly 1000, 999, 1001).

### 6. Resume prompt
In the `gens-availability-tracker` repo, we just fixed a silent bug in `apply_strict_id_gate()` in `checker.py` that caused generators with IDs ≥ 1000 to be skipped incorrectly. The fix is in place but untested. Next: add a fixture to `tests/fixtures/` and write a unit test for the ID gate boundary logic before running the full pipeline.
```

---

Run with: /resume-note
