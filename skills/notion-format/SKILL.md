---
name: notion-format
description: Apply Notion formatting changes consistently across ALL tabs and sections in a single pass, including executive brief pipeline rules.
---

Trigger when: user asks to format, clean up, or standardize a Notion page, executive brief, or any multi-tab document.

When formatting a Notion file or executive brief:

1. List every tab and section in the target file before touching anything
2. Apply these formatting rules to every section in one pass — do not do them one at a time:
   - Use H2 (`##`) for all section headings
   - Bold the first phrase or label at the start of each bullet point
   - Remove all color highlights unless explicitly specified by the user
   - Remove any empty or blank sections entirely
3. Apply all other heading, color, bold, and rename changes to each tab in the same pass
4. After applying, grep for any remaining old patterns to verify nothing was missed
5. Confirm no tab or section was skipped before marking done
6. Commit and push only after all tabs are verified
