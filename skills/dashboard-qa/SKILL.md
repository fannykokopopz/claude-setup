---
name: dashboard-qa
description: Validate dashboards, sheets, and multi-view UI changes
---

## Trigger Conditions

Trigger when:
- Changes are made to dashboards, Google Sheets, Notion views, or any multi-tab UI
- Formatting, layout, or styling is modified in any view
- Data pipelines, calculations, or aggregations are updated
- Filters, dropdowns, or controls are added or changed
- Before marking any frontend or data change as done

> CLAUDE.md rule: Test ALL tabs/views after any change — never assume only the edited view is affected.

---

## Step-by-Step QA Process

### 1. Identify Scope
- List every tab, view, or section that could be affected (not just the one edited)
- Include any views that share the same data source, filter logic, or layout components

### 2. Layout and Formatting Check
- Confirm headings, column widths, and row spacing are consistent across all tabs
- Check that bold, color highlights, and font sizes match the intended hierarchy
- Verify no truncated text, overflowing cells, or misaligned columns
- Confirm mobile/responsive layout if applicable

### 3. Data Integrity Validation
- Re-run or refresh all computed metrics and confirm values match expected results
- Spot-check calculations: pick 2-3 rows and manually verify the formula output
- Confirm aggregations (sums, averages, counts) are scoped correctly (no off-by-one in ranges)
- Check that date filters and range selectors return the correct subset of data
- Verify that data from external sources (APIs, Sheets imports) loaded correctly

### 4. Controls and Interactivity
- Test each filter and dropdown: confirm it narrows data correctly and resets cleanly
- Verify sort order is correct and stable after filtering
- Confirm any buttons, links, or action triggers behave as expected

### 5. Regression Check
- Compare key metrics against the previous known-good state
- Look for values that are unexpectedly zero, null, or changed by more than a small margin
- Check that no previously working tab or view broke as a side effect

### 6. Edge Cases
- Empty state: what renders when there is no data?
- Partial data: does the view handle missing fields or null values gracefully?
- Boundary dates: does the view handle month/year rollovers, or start-of-period edge cases?
- Large data: does the view stay performant and readable with max expected rows?

### 7. Final Sign-Off
- Only mark the change as done after all tabs/views pass
- Flag any issues with a clear PASS / FAIL / FLAG status per view (see output format below)

---

## Output Format

Produce a QA report in this format after each run:

```
Dashboard QA Report — [Date] — [Change Description]

| Tab / View         | Layout | Data | Controls | Edge Cases | Status |
|--------------------|--------|------|----------|------------|--------|
| Overview           | PASS   | PASS | PASS     | PASS       | PASS   |
| Weekly Breakdown   | PASS   | FLAG | PASS     | PASS       | FLAG   |
| Filters Panel      | PASS   | PASS | FAIL     | N/A        | FAIL   |

Flags / Issues:
- Weekly Breakdown > Data: Row totals differ by ~2% from manual calculation — needs review
- Filters Panel > Controls: Date range reset button does not clear end date field

Next Steps:
- Fix filters panel reset logic
- Confirm Weekly Breakdown formula range with data owner
```

Status key:
- PASS — checked and correct
- FLAG — works but has a concern worth reviewing
- FAIL — broken or incorrect, must fix before marking done
- N/A — not applicable to this view

---

Run with: /dashboard-qa
