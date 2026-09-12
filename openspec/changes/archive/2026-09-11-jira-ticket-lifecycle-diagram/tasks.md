## 1. Confirm topology before authoring

- [x] 1.1 Walk the design.md assumptions checklist with the user: reopen origin (ACCEPTANCE → OPEN), QA-reject edge IN QA → READY FOR DEVELOPMENT, UAT REVIEW entry/exit, and the bidirectional pairs (confirmed both-way by user, 2026-09-11). Verify: a settled edge list exists covering all arrows in screenshot 1 before any JSON is written.

## 2. Author the lifecycle candidate

- [x] 2.1 Create `docs/archify/jira-ticket-lifecycle.lifecycle.json` (archify lifecycle schema v1): 12 states with ids, types, sublabels (plain-language "who acts / what the ticket waits for"), per-state board-column tags, 3 lanes (main, work, terminal), all transitions as two directed edges for each confirmed both-way pair, labeled loops (QA rejected, Reopen, Ready for Publishing gate), `meta.quality_profile: "standard"`, `meta.animation: "trace"`, `meta.locale: "en"`, 3–5 guided views, and ≤3 closing cards (status↔board-column map + board conventions). Verify: `node .agents/skills/archify/bin/archify.mjs validate lifecycle docs/archify/jira-ticket-lifecycle.lifecycle.json --quality standard --json` exits 0 (0 composition errors; warnings OK).
- [x] 2.2 Fix any composition errors using compiler receipt diagnostics. Verify: standard validation passes with 0 errors.

## 3. Deliver and verify the HTML

- [x] 3.1 Deliver: `node .agents/skills/archify/bin/archify.mjs deliver lifecycle docs/archify/jira-ticket-lifecycle.lifecycle.json docs/jira-ticket-lifecycle.html --quality standard --json`. Verify: exit 0, receipt with SHA-256, file exists at `docs/jira-ticket-lifecycle.html`.
- [x] 3.2 Junior walkthrough: open the HTML; use trace animation by clicking IN QA (shows forward + reject flows animate), open the "Publish path" guided view, search a status, read the board-mapping card. Verify: animation works, all12 statuses visible, reopen edge visible.

## 4. Wrap up

- [x] 4.1 Summarize delivery receipt and note any unverified assumption (reopen origin) for follow-up in Jira.
