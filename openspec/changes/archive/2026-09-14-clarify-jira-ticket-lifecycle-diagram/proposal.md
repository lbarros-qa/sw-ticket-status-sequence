## Why

The current `docs/jira-ticket-lifecycle.html` is hard for a QA junior or business reader to follow: Archify `lifecycle` stacked waiting gates under the wrong phases, so arrows overlap, and the picture tries to show every reverse edge from the Jira workflow screenshot. Onboarding needs one obvious happy path, the QA-fail return, reopen, and live check as the last gate before production.

## What Changes

- Rebuild the standalone HTML as a teaching diagram (Archify `workflow` v2) instead of a crowded `lifecycle` status machine.
- Keep all 12 statuses. Draw only Option A arrows: the complete happy path, QA fail (IN QA → READY FOR DEVELOPMENT), reopen (CLOSED → OPEN), and live check (ACCEPTANCE / LIVE CHECK) as a required stop before production close.
- Drop the dense bidirectional mesh and the invented "Live check failed" return that is not in the Jira workflow screenshot.
- Align node labels with board names where they differ (READY FOR DEV, UAT FEEDBACK, IN DEV, READY FOR PUBLISH..., LIVE CHECK) so the picture matches the dashboard.
- Raise delivery quality to Archify showcase (0 composition errors, 0 warnings) so connectors do not overlap or share corridors.
- Keep one self-contained HTML at `docs/jira-ticket-lifecycle.html` with guided views and trace animation.

## Capabilities

### New Capabilities

<!-- none -->

### Modified Capabilities

- `jira-ticket-lifecycle`: Change the audience, diagram type, visible transitions, live-check-before-production gate, and validation quality of the onboarding HTML.

## Impact

- Replaces `docs/archify/jira-ticket-lifecycle.lifecycle.json` with a workflow v2 candidate and regenerates `docs/jira-ticket-lifecycle.html`.
- No Jira workflow, board, or runtime code changes.
- Existing main spec requirements for "every allowed transition" and `lifecycle` type are superseded by this delta.
