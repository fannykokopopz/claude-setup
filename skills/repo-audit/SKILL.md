---
name: repo-audit
description: Audit a repo before making any changes
---

Trigger when: user says "audit this repo", "look at the codebase before starting", or asks for a structured review before any code changes.

1. List all top-level directories and files
2. Read README.md, CLAUDE.md, and any config files
3. Identify stack: framework, package manager, test runner, lint setup
4. Locate entry points and main execution flow
5. Find closest existing patterns relevant to the task
6. Check for risks:
   - duplicate functions
   - merge conflict markers
   - inconsistent naming or structure
   - dead code
   - missing tests
   - outdated dependencies
   - security vulnerabilities
7. Identify how to validate changes (tests, scripts, manual checks)
8. Output a prioritized TODO list grouped by: Critical, High, Medium, Low
   - For each item, include file path and 1-line fix description
9. Recommend lowest-risk implementation approach before editing