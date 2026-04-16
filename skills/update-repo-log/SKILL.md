---
name: update-repo-log
description: Generate a ready-to-paste repo log row for the 3 - REPO LOG spreadsheet. Covers all columns: Project/Repo, Links, What It Is, Stack, API Keys Needed, Est. Cost per Run, Status, Last Done (date), Last Done (what), Next Up, Blockers.
---

Trigger when: user asks to log, document, or update the repo log entry for the current project.

Produce a single repo log row for the **3 - REPO LOG — LAST DONE & NEXT UP** spreadsheet.

## Steps

1. **Identify the repo** — use the current working directory name and remote URL (`git remote get-url origin`)
2. **Read the repo** — scan entry point (main.py / index.ts / etc.), README if present, and folder structure to understand what it does in one sentence
3. **Extract stack** — language, framework, runtime, deployment target (e.g. GitHub Actions cron, Vercel, local)
4. **Extract API keys** — grep for `os.environ`, `process.env`, `secrets.`, or `.env` references; list every distinct key name
5. **Estimate cost per run** — if the repo calls an AI API, note the model and rough token usage; otherwise write "—"
6. **Check git log** — `git log -1 --format="%ad | %s" --date=format:"%d %b %Y"` for last commit date and message
7. **Infer Next Up and Blockers** — from any TODO/FIXME comments, open issues mentioned in code, or context from the current session; write "—" if none

## Output format

Print a clean markdown table with these exact columns, one data row only — no extra commentary before or after:

| Column | Value |
|--------|-------|
| **Project / Repo** | [repo name / short label] |
| **Links** | [github.com/user/repo] |
| **What It Is** | [one sentence: what it does end-to-end] |
| **Stack** | [comma-separated: language, key libs, deployment] |
| **API Keys Needed** | [comma-separated list of env var names] |
| **Est. Cost per Run** | [e.g. ~$0.10–0.20 or —] |
| **Status** | Active / Paused / WIP |
| **Last Done (date)** | [DD Mon YYYY] |
| **Last Done (what)** | [one line from git log or session context] |
| **Next Up** | [next task or —] |
| **Blockers** | [blocker or —] |
