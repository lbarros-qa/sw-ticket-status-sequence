## Why

New junior engineers joining the web content team must learn the Jira ticket workflow from a static screenshot (12 statuses, several rejection/reopen loops) and separately figure out how the real board columns (READY FOR DEV, IN DEV, READY FOR QA, IN QA, IN UAT, READY FOR PUBLISH..., LIVE CHECK) relate to those statuses. The screenshot has ambiguous arrows, no per-state explanations, and no bridge to the board, which makes onboarding slow and error-prone.

## What Changes

- Add a standalone, interactive HTML lifecycle diagram (authored with the bundled `/archify` skill, `lifecycle` diagram type) that models the ticket as a state machine:
  - All 12 workflow statuses: OPEN, IN ANALYSIS, READY FOR INTAKE, READY FOR DEVELOPMENT, IN DEVELOPMENT, READY FOR QA, IN QA, UAT REVIEW, IN UAT, READY FOR DEPLOYMENT, ACCEPTANCE, CLOSED.
  - Labeled transitions: forward path, QA rejection loop, UAT review entry/exit, publish gate, reopen path.
  - One short plain-language description per status (what the ticket is waiting for, who acts).
- Add a mapping between workflow statuses and the board columns observed on the dashboard, plus the board conventions juniors will see (`{blocked}`, `{waiting for other tickets to go live}` prefixes, flags).
- Provide guided views for common junior questions ("What can happen after IN QA?", "How does a ticket get reopened?", "Publish path").
- Output is a single self-contained HTML file in `docs/`, validated to archify showcase quality.

## Capabilities

### New Capabilities
- `jira-ticket-lifecycle`: The interactive status-machine diagram of the Jira ticket workflow, its per-status guidance, and its board-column mapping for junior onboarding.

### Modified Capabilities

<!-- None: no existing capabilities in openspec/specs. -->

## Impact

- No runtime code, APIs, or dependencies change. The repo currently has no source.
- Adds `docs/jira-ticket-lifecycle.html` (deliverable) and an archify candidate JSON spec under `docs/archify/` used to generate it.
- Onboarding material only; workflow itself in Jira is unchanged.
