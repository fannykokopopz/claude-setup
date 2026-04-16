---
name: implementation-plan
description: Turn a request into a clean, low-risk execution plan
---

## Trigger Conditions

Use this skill when:
- The user asks to implement a feature, build something new, or add functionality
- The task involves multiple files, systems, or non-obvious sequencing
- Before starting any non-trivial work where the approach isn't immediately clear
- The user says "plan this", "how should I approach", or "before we start"

Do NOT use for:
- Trivial one-line changes (renaming a variable, fixing a typo, updating a constant)
- Purely exploratory questions that don't require code changes
- Tasks with an obviously single step

---

## Steps

1. **Restate the task in engineering terms**
   - Strip ambiguity. What exactly needs to change, and in which system/file?

2. **Audit existing patterns first**
   - Use Grep/Glob to find similar existing implementations before proposing anything new
   - Identify the framework, package manager, test runner, and lint commands in use
   - Check for helper functions or utilities that already solve part of the problem

3. **Define expected outcome and success criteria**
   - What does "done" look like? (UI state, API response, file output, test pass)
   - What should NOT change (regressions to guard against)?

4. **Break into small, ordered steps**
   - Each step should be independently verifiable
   - Flag which steps have dependencies on prior steps
   - Prefer minimal diffs — edit existing files before creating new ones

5. **Identify dependencies, assumptions, and edge cases**
   - External APIs, environment variables, or secrets required
   - Assumptions about data shape, auth state, or system state
   - Edge cases that could break the happy path

6. **Highlight risks and where things may break**
   - Which step is highest risk? Why?
   - What's the rollback strategy if something goes wrong?

7. **Define validation steps**
   - Exact commands to run (tests, lint, type-check)
   - UI checks or API responses to verify
   - Any dry-run options available before committing

---

## Example Output

```
Task: Add rate-limit error handling to the Serper API integration

Outcome: When Serper returns 429, the checker retries after a backoff delay instead of crashing. Existing behavior for 200/404 responses is unchanged.

Steps:
1. Read checker.py to find where the Serper API call is made (Grep: "serper" or "requests.get")
2. Wrap the API call in a try/except that catches HTTPError with status 429
3. Add exponential backoff (e.g., wait 2^attempt seconds, max 3 retries)
4. Log a warning on each retry; raise after max retries exceeded
5. Update any tests that mock the Serper response to cover the 429 case

Dependencies:
- `requests` library already in use (confirmed by import)
- No new env vars required

Assumptions:
- Serper always returns JSON on non-429 errors (verify with a test call if uncertain)

Risks:
- Backoff logic could cause the GitHub Actions job to time out if retries are long
  Mitigation: cap total wait at 30s, not per-retry

Validation:
- Run: python -m pytest tests/ -v
- Manually trigger with a mocked 429 response and confirm retry + eventual raise
- Check GitHub Actions log for warning messages on retry
```

---

Run with: /implementation-plan
