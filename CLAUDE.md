# Global Working Rules

## External APIs
- When integrating external APIs (Serper, Google CSE, etc.), first verify the API works with a minimal test call before building features on top of it.
- If an API fails, propose an alternative immediately rather than debugging endlessly.

## How to work
- For anything non-trivial, start by understanding the repo before changing code.
- Prefer minimal diffs over large rewrites.
- Follow existing architecture, patterns, naming, and folder conventions.
- Before editing, identify framework, package manager, test runner, and lint commands.
- Before declaring work done, run the relevant validation commands if available.

## Before Editing
- Before editing a file, check for duplicate function definitions or merge conflict markers.
- Run a quick grep for:
  - `<<<<`
  - `====`
  - `>>>>`
- Also check for duplicate `def ` signatures in the target file.

## Code Quality
- Keep changes scoped and intentional.
- Do not introduce unnecessary abstractions.
- Match existing code style and patterns exactly.
- Avoid refactoring unrelated parts of the codebase.

## Testing
- Always validate changes using the repo's existing test or lint commands where available.
- When making formatting/UI changes across multiple tabs or views:
  - Test ALL tabs/views after each change
  - Do not assume only the edited view is affected
  - Watch for regressions in layout, formatting, and data display

## Frontend Design
- Stack: Next.js deployed on Vercel, Supabase as backend.
- Prefer React Server Components where possible; use client components only when interactivity requires it.
- Use Tailwind CSS utility classes for styling — avoid inline styles and custom CSS unless unavoidable.
- Follow a mobile-first approach: design for small screens first, then scale up with responsive breakpoints.
- Keep components small and single-responsibility. Co-locate component styles and logic.
- Use Supabase client only in server-side code or API routes — never expose keys in client components.
- Validate UI across all views/tabs after any layout or styling change (not just the one being edited).
- Ensure interactive elements meet basic accessibility standards: keyboard navigable, sufficient color contrast, ARIA labels where needed.
- When building dashboards or data views, confirm data loading states and empty states are handled visually.
- Match Vercel's deployment constraints: avoid large client bundles, prefer static generation or ISR over SSR where possible.

## Staged Development
- For multi-component features, implement and verify one stage at a time before moving on.
- Do not proceed to the next stage until the current one is confirmed working.
- Suggested stages: API integration → data processing → single view → all views.
- This prevents cascading bugs that are harder to diagnose when everything is built at once.

## Output Style
- Be direct and concise.
- Explain tradeoffs briefly.
- Flag risks early.
- If something is uncertain, say so instead of guessing.

## Preferred Workflow
- Inspect repo structure first.
- Find the closest existing pattern before creating a new one.
- Keep changes minimal and reversible.
- Suggest next steps only when they are useful.
