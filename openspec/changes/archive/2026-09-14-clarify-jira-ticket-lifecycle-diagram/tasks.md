## 1. Author the workflow candidate

- [x] 1.1 Write `docs/archify/jira-ticket-lifecycle.workflow.json` as Archify workflow schema_version 2 with all 12 status ids, the six who-acts lanes and column placement from design.md, board-name labels where they differ, and sublabels that name who acts — verify `node .claude/skills/archify/bin/archify.mjs validate workflow docs/archify/jira-ticket-lifecycle.workflow.json --quality showcase --json` reports a parseable document (schema errors, if any, are only composition/geometry, not missing required fields)
- [x] 1.2 Set `mainPath` to the happy path OPEN → … → ACCEPTANCE → CLOSED, add only Option A edges (happy path + labeled QA fail IN QA → READY FOR DEVELOPMENT + labeled Reopen CLOSED → OPEN), omit reverse mesh and Live-check-failed, and set `meta.quality_profile` to `"showcase"`, `locale` `"en"`, `animation` `"trace"`, 3–5 guided views, and ≤3 cards — verify the JSON contains exactly those edges and `grep -c '"from"'` on the edges array matches the Option A count (11 happy-path + 2 loops)
- [x] 1.3 Validate at showcase and repair one diagnosed subject at a time until the receipt has all artifact checks, 0 composition errors, and 0 warnings — verify `node .claude/skills/archify/bin/archify.mjs validate workflow docs/archify/jira-ticket-lifecycle.workflow.json --quality showcase --json` exits 0 with those counts

## 2. Deliver the HTML

- [x] 2.1 Run `node .claude/skills/archify/bin/archify.mjs deliver workflow docs/archify/jira-ticket-lifecycle.workflow.json docs/jira-ticket-lifecycle.html --quality showcase --json` and verify the command exits 0, the HTML exists, and the receipt lists SHA-256 for both specification and artifact
- [x] 2.2 Run `node .claude/skills/archify/bin/archify.mjs visual-check docs/jira-ticket-lifecycle.html --json` on the delivered file (do not rerender) and verify the command completes with browser-evidence output for the default view
- [x] 2.3 After a successful deliver, delete `docs/archify/jira-ticket-lifecycle.lifecycle.json` and `docs/archify/jira-ticket-lifecycle-polished.lifecycle.json` if present — verify `docs/archify/` contains the `.workflow.json` source and no leftover `.lifecycle.json`

## 3. Check teaching behavior

- [x] 3.1 Open the HTML and confirm all 12 nodes are visible, the happy path including LIVE CHECK sits before CLOSED, QA fail and Reopen are the only extra arrows, and no connector overlaps a node or another label — verify by reading the candidate JSON edges plus the visual-check screenshots (or an equivalent desktop look at 1440×900)
- [x] 3.2 Confirm guided views for happy path / live check, QA fail, and reopen focus the statuses named in the delta spec — verify each view’s `focus` array in the candidate JSON matches those statuses
