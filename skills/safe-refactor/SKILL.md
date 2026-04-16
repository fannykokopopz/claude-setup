---
name: safe-refactor
description: Refactor code without breaking existing behavior
---

## Trigger Conditions

Trigger when the user says any of the following:
- "refactor", "clean up", "clean this up", "restructure"
- "improve code quality", "make this cleaner", "simplify this"
- "remove duplication", "deduplicate", "consolidate"
- Asks to improve internal implementation without changing what the code does

Do NOT trigger if the user is explicitly asking to change behavior, add features, or modify public APIs — those require a different approach.

---

## Pre-Flight Checklist

Before making any changes, confirm the following:

- [ ] You have read the target file(s) in full — not just the function being refactored
- [ ] You have searched for merge conflict markers (`<<<<`, `====`, `>>>>`) and duplicate `def ` signatures
- [ ] You know what tests or validation commands exist (`pytest`, `npm test`, lint, etc.)
- [ ] You have identified the public interface (exported functions, class methods, CLI entrypoints, API routes)
- [ ] You understand the current behavior well enough to describe it in one sentence

If any item is unchecked, complete it before proceeding.

---

## Step-by-Step Instructions

### Step 1 — Understand Current Behavior

Read the target file(s) fully. Write a one-sentence description of what the code does and what its key outputs are. If you cannot describe the behavior confidently, read more context before changing anything.

Look for:
- Return values and side effects (writes to disk, API calls, mutations)
- Error handling paths
- Any branching logic that is non-obvious

### Step 2 — Map All Usage Points

Run a grep to find every place the target function, class, or module is used:

```bash
grep -rn "function_name\|ClassName\|module_name" . --include="*.py"
```

Also check:
- Import statements (`from x import y`, `import x`)
- Dynamic references (e.g., string-based lookups, `getattr`, config-driven dispatch)
- Tests that call the code directly

List every call site before touching anything.

### Step 3 — Define What Must Not Change

Explicitly state the public interface that must be preserved:
- Function signatures (name, parameters, defaults, return type)
- Class method signatures and attributes used externally
- CLI argument names and output format
- API route paths and response schema
- Any behavior tests are asserting

If a call site passes positional args, the parameter order is part of the contract.

### Step 4 — Plan the Refactor Incrementally

Break the refactor into the smallest safe steps. Each step should be independently reviewable. Examples of good step granularity:
- Extract one repeated block into a helper function
- Rename an internal variable for clarity
- Remove one dead code branch
- Consolidate two similar loops into one

Do not combine unrelated changes in a single diff. Do not rename public symbols without updating all call sites in the same step.

### Step 5 — Execute One Step at a Time

For each planned step:
1. Make the minimal edit
2. Verify the public interface is unchanged
3. Run tests or a dry-run if available
4. Confirm the output/behavior matches the pre-refactor baseline

Stop and report to the user if any step produces unexpected test output.

### Step 6 — Check for Dead Code and Duplication

After the primary refactor, scan for:
- Unused imports (`grep -n "^import\|^from" file.py` and check each)
- Functions defined but never called (grep the function name across the codebase)
- Duplicate logic that can now be consolidated given the refactored structure

Only remove dead code you are confident is unused. When in doubt, leave it and flag it.

### Step 7 — Validate No Regressions

Run the full test suite or available validation command:

```bash
# Python
pytest

# Node/TypeScript
npm test

# Lint
flake8 . / eslint .
```

If no automated tests exist, manually trace the key execution paths and compare outputs to the pre-refactor behavior you documented in Step 1.

---

## Example: Safe Refactor Plan

**Target:** `checker.py` — the `run_checks()` function has three nearly identical loops.

**Current behavior:** Iterates over three lists of items, calls `check_item()` on each, and appends results to `self.results`.

**Public interface to preserve:** `run_checks(self, items: list[str]) -> list[dict]`

**Refactor plan:**
1. Extract the shared loop body into a private helper `_check_and_collect(item)` — no change to public API
2. Replace the three loops with a single loop calling `_check_and_collect` — verify results list is identical
3. Remove the now-dead intermediate variables — confirm no external code referenced them (grep confirmed: none)

**Validation:** `pytest tests/test_checker.py` — all 12 tests pass before and after.

---

## What a Safe Refactor Does NOT Do

- Change parameter names or order in public functions
- Alter return types or response shapes
- Remove functionality "that looks unused" without grepping to confirm
- Rewrite large sections in one diff
- Touch files outside the stated scope without flagging it first

---

Run with: /safe-refactor
