---
name: apps-script-patch
description: Work on Google Apps Script and Sheets carefully, with minimal-risk changes and validation focus.
---

Trigger when: user asks to edit Google Apps Script, modify a Google Sheet formula or script, work with Sheets triggers or menus, or debug Apps Script behavior.

---

## Pre-Flight Checklist

Before touching any code or formula:

1. **Read the script header** — identify the project name, any `const` spreadsheet IDs, and sheet name references.
2. **List all sheet tabs** — note exact tab names (case-sensitive). Never assume a tab name; ask if uncertain.
3. **Check for existing triggers** — look for `ScriptApp.newTrigger(...)` calls or `onOpen`, `onEdit`, `onChange` function definitions. Flag any duplicates or conflicting trigger registrations.
4. **Identify shared state** — look for global variables or `PropertiesService` usage that other functions may depend on.
5. **Note timezone assumptions** — if the script uses `new Date()`, `Utilities.formatDate()`, or cron-style triggers, confirm the spreadsheet timezone matches expectations.

---

## Step-by-Step Patch Process

### 1. Scope the change
- Identify the exact function(s) to modify. Do not edit unrelated functions.
- If the request is ambiguous, state your interpretation before writing code.

### 2. Validate inputs and ranges
- Before writing to a range, confirm the column/row indices match the actual sheet headers.
- Never use hardcoded row numbers (e.g., row 2) without noting that assumption explicitly.
- Use `getLastRow()` / `getLastColumn()` defensively; check for empty sheets before iterating.

### 3. Check for formula conflicts
- If adding or modifying a formula in a cell, confirm no existing formula or data validation will be overwritten silently.
- For `ARRAYFORMULA` or `IMPORTRANGE`, note that these can block writes to dependent cells.

### 4. Triggers — duplication risk
- Installing a trigger inside `onOpen` or without a guard causes duplicates on every open.
- Always pair trigger installation with a check: `ScriptApp.getProjectTriggers()` before `newTrigger(...)`.
- Prefer time-based triggers set up once via a separate install function, not on every script run.

### 5. Menus
- Custom menus must be created in `onOpen(e)`. Confirm the function is not already registered elsewhere.
- Use `SpreadsheetApp.getUi().createMenu(...)` only once per `onOpen` to avoid duplicate menu entries.

### 6. Apply the smallest viable patch
- Change only the lines required. Do not reformat unrelated code.
- If refactoring is needed to make the change safe, flag it separately as a follow-up — do not bundle it.

---

## Caution: Destructive Operations

Flag and get explicit confirmation before proceeding if the change involves:

- `clearContent()`, `clearContents()`, or `clear()` on any range
- Overwriting an entire column or row that may contain existing data
- Deleting rows with `deleteRow()` or `deleteRows()`
- Replacing a formula that feeds other calculated columns
- Resetting `PropertiesService` keys used elsewhere in the script

State exactly which cells/ranges will be affected and whether data can be recovered (e.g., via Sheet history).

---

## Output Format

After making or proposing changes, return:

1. **Diff of changed functions** — show only the modified functions with a brief note on what changed and why.
2. **Assumptions made** — list any tab names, column positions, or trigger setups you assumed.
3. **Manual test steps** — numbered steps the user can run to verify the change works:
   - Which tab to open
   - What action to perform (e.g., open the sheet, run a menu item, edit a cell)
   - What the expected result is
4. **Risks or follow-ups** — flag anything that was out of scope but should be addressed (e.g., duplicate triggers found, stale named ranges).

---

Run with: /apps-script-patch
