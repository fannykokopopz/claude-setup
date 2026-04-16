---
name: audit-skills
description: Audit and rebuild all Claude skill files in ~/.claude/skills/ using parallel sub-agents. Evaluates completeness, rewrites incomplete skills, and produces a SKILLS_AUDIT.md summary.
---

Trigger when: user asks to audit, review, or rebuild all skill files in ~/.claude/skills/.

Audit and rebuild all Claude skills autonomously. Do not ask for input — use best judgment and document assumptions.

## Steps

1. List every `.md` file in `~/.claude/skills/` and read each one

2. For each skill, spawn a sub-agent with this mandate:
   a. Evaluate whether the skill file has:
      - Clear trigger conditions (when to use this skill)
      - Structured step-by-step instructions
      - Example output or usage
   b. If the skill is empty or incomplete, rewrite it based on:
      - The filename and directory name
      - Any related code found in the user's repos
      - Best practices for Claude skill definitions
   c. Simulate an invocation — write a test prompt and verify the skill instructions would produce a coherent response
   d. Report back: skill name, status (`valid` / `rewritten` / `needs-human-input`), and a summary of changes

3. Run all skill sub-agents in parallel

4. After all sub-agents complete, write `~/.claude/skills/SKILLS_AUDIT.md` with:
   - A table of all skills: name | status | what changed
   - Any skills flagged as `needs-human-input` with explanation

5. Commit all updated skill files:
   - Message: `chore(skills): audit and rebuild all skill definitions`
   - Push to remote if a remote exists

Run with: /audit-skills
