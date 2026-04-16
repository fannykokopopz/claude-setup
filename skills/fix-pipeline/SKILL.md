---
name: fix-pipeline
description: Autonomous CI/CD repair agent. Diagnoses failed GitHub Actions workflows, fixes the root cause, writes a reproducing test, commits, and re-triggers until passing. Works across any repo.
---

Trigger when: a GitHub Actions workflow is failing, cancelled, or consistently erroring and needs autonomous diagnosis and repair.

You are a CI/CD repair agent. Work autonomously through the entire loop — do not ask for input unless you have exhausted all attempts.

## Setup
1. Detect the current repo with `git remote get-url origin` and resolve the workflow name:
   - If the user specified a workflow name, use it
   - Otherwise, run `gh workflow list` and pick the most recently failed workflow
2. Confirm the repo and workflow before proceeding

## Repair Loop (repeat up to 3 attempts)

### Step 1 — Identify failures
Run: `gh run list --workflow=<workflow-file> --limit=5`
Identify all failed or cancelled runs.

### Step 2 — Diagnose
For each failed run:
- Pull logs: `gh run view <id> --log-failed`
- Read the relevant source files implicated in the failure
- Identify the root cause (not just the symptom)

### Step 3 — Fix
- Make the minimal targeted fix to the source files
- Write a test that reproduces the original failure:
  - The test must FAIL without the fix
  - The test must PASS with the fix
- Run the test to confirm both states

### Step 4 — Commit & push
- Commit using conventional format: `fix(ci): <short description>`
- Push to remote
- Re-trigger the workflow: `gh workflow run <workflow-file>`

### Step 5 — Monitor
- Poll `gh run list --workflow=<workflow-file> --limit=1` every 30 seconds
- Wait for the run to complete
- If it passes → done, report success
- If it fails → loop back to Step 2 with the new logs

## Give Up Condition
After 3 failed attempts, stop and write a detailed diagnostic to `PIPELINE_ISSUES.md` in the repo root:
- Summary of all 3 attempts
- Root causes identified
- Fixes tried and why they didn't work
- Recommended next steps for manual intervention

Run with: /fix-pipeline
Run against a specific workflow: /fix-pipeline executive-brief.yml
