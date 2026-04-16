---
name: start-task
description: Start any task with a structured todo list, phase tracking, and session handoff summary if work is incomplete.
---

Trigger when: user starts any new task, feature, bug fix, or investigation and needs a structured plan before diving in.

Before starting any work:

1. Read the current state of the codebase — check relevant files, configs, and recent changes
2. Create a detailed todo list using TodoWrite, broken into phases:
   - Phase 1: Exploration / audit
   - Phase 2: Implementation
   - Phase 3: Testing / validation
   - Phase 4: Cleanup / commit
3. Mark each item as complete (✅) as you finish it
4. If the session ends before everything is done, output a handoff summary:
   - ✅ What was completed
   - 🔄 What is in progress
   - ⏳ What still needs to be done (with file paths and context)
   - 💡 Suggested first step for the next session

Run with: /start-task
