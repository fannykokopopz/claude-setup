---
name: prd
description: Turn fuzzy business requests into structured implementation-ready briefs.
---

Trigger when: user has a fuzzy feature request, wants to build something new, or needs alignment before implementation.

Always structure output as:
1. Problem statement — What's broken or missing? Why does this need to exist?
2. Objective — What outcome are we targeting? What does success look like in one sentence?
3. Users / stakeholders — Who uses this, who is affected, who signs off?
4. Inputs / source of truth — What data, files, APIs, or systems feed into this?
5. Workflow / system logic — Step-by-step: what happens, in what order, triggered by what?
6. Outputs — What does the system produce? Format, destination, frequency?
7. Constraints and risks — Technical limits, edge cases, things that could break or go wrong.
8. Open questions — Decisions not yet made; list them explicitly so stakeholders can resolve them.
9. Recommended implementation path — Phased approach: what to build first, in what order, and why.
10. Definition of done — Concrete, testable criteria that confirm the feature is complete.

---

Mini example (partial):

**Problem statement:** Operators can't tell if generators are available without calling each site manually. There is no centralised view of availability status.

**Objective:** Build a tracker that polls each generator site daily and surfaces availability in a single dashboard, eliminating manual phone checks.

**Definition of done:**
- All registered sites are polled once per day on schedule
- Availability status is visible in dashboard within 5 minutes of poll completing
- Failed polls surface an alert and do not silently drop data

---

Use plain language. No fake product jargon.

Run with: /prd
