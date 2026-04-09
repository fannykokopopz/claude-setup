---
name: apps-script-patch
description: Work on Google Apps Script and Sheets carefully, with minimal-risk changes and validation focus.
---

When editing Apps Script or Google Sheets support code:
1. Prefer smallest viable patch
2. Preserve existing sheet structure unless explicitly told otherwise
3. Call out assumptions about tabs, headers, IDs, and timezones
4. Validate formulas, ranges, menu actions, and triggers
5. Return changed functions and manual test steps
