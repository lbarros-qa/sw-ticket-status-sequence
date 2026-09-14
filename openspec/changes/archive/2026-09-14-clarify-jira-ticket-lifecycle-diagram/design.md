## Context

The live artifact is `docs/jira-ticket-lifecycle.html`, generated from `docs/archify/jira-ticket-lifecycle.lifecycle.json` (Archify `lifecycle` v1, `quality_profile: standard`). That renderer pins waiting/terminal columns under later main-rail columns, which stacked READY FOR QA / READY FOR DEVELOPMENT / READY FOR INTAKE under IN DEVELOPMENT and forced `via` detours. See proposal.md for why that fails a QA junior / business reader.

Constraints: Archify workflow schema v2 (`cols` 0–5, `mainPath`, lane-based layout, edge `role` `main|return|error`); bundled CLI `node .claude/skills/archify/bin/archify.mjs`; existing capability `jira-ticket-lifecycle`. Workflow node `type` is a component enum (`frontend`, `backend`, `security`, …); it does not carry lifecycle state kinds.

## Goals / Non-Goals

**Goals:**
- One workflow v2 candidate whose default view is Option A (happy path + QA fail + reopen + live check before close).
- Layout that a reader can scan as who-acts × time, matching the board names they see.
- Showcase delivery: 0 composition errors, 0 warnings, no overlapping connectors.

**Non-Goals:**
- Modeling every reverse arrow from the Jira workflow screenshot (OPEN ↔ IN ANALYSIS, IN QA ↔ IN UAT, Any → CLOSED, and similar).
- A "Live check failed" return to READY FOR DEPLOYMENT.
- Changing Jira, the board column order, or adding a second HTML file.

## Decisions

1. **Diagram type: Archify `workflow` schema_version 2, not `lifecycle`.**
   Lifecycle's event/terminal column N sits under main column N+2, which caused the overlap. Workflow v2 is a readable lane×column grid with `mainPath` and labeled return/error edges. Alternative considered: keep lifecycle and add more `via` points — rejected; showcase still forbids crossings in that grid.

2. **Lanes = who acts; columns = time (0–5).** Six lanes, one node per (lane, col):

   ```
           col 0     col 1         col 2           col 3        col 4              col 5
   intake  OPEN      IN ANALYSIS   READY FOR INTAKE
   dev                             READY FOR DEV   IN DEV
   qa                                              READY FOR QA IN QA
   uat                                                          UAT FEEDBACK       IN UAT
   publish                                                      READY FOR PUBLISH  LIVE CHECK
   done                                                                            CLOSED
   ```

   Happy-path edges stay on or next to the staircase (down-right). QA fail is IN QA → READY FOR DEV (`role: "error"` or `"return"`, `variant: "dashed"`). Reopen is CLOSED → OPEN (`role: "return"`, `variant: "dashed"`, prefer `route: "bottom-channel"` only if auto routing fails validation). Alternative considered: one horizontal spine of 12 columns — rejected; schema max col is 5.

3. **Board name is the node `label` when it differs from the workflow status.** IDs stay the 12 status ids (`ready_for_development`, `acceptance`, …). Labels: READY FOR DEV, IN DEV, UAT FEEDBACK, READY FOR PUBLISH..., LIVE CHECK. Off-board statuses keep workflow names (OPEN, IN ANALYSIS, READY FOR INTAKE, CLOSED). Sublabels still say who acts. Alternative: keep workflow names and only tag the board — rejected; the reader’s daily UI is the board.

4. **`mainPath` is the 12-status happy path including ACCEPTANCE.** That makes live check a required stop before CLOSED in the compiler’s happy-path contract, not only in copy. `semanticChecks.requiredEdges` may pin QA fail and reopen if the authoring pass needs them.

5. **Authoring loop:** new file `docs/archify/jira-ticket-lifecycle.workflow.json`; `meta.quality_profile: "showcase"`; omit `visual_preset` and `subtitle`; `locale: "en"`; `animation: "trace"`; 3–5 `meta.views` (happy path / live check, QA fail, reopen). Start with automatic routes; add at most one diagnosed geometry control per repair. Deliver to `docs/jira-ticket-lifecycle.html`. Remove the old `.lifecycle.json` (and the unused `jira-ticket-lifecycle-polished.lifecycle.json` if still present) after a successful deliver so the workflow JSON is the only source.

6. **Cards (≤3):** board column map (including off-board statuses), board conventions (`{blocked}`, `{waiting for other tickets to go live}`, flags), and how to read the two loops (QA fail, reopen). No extra loop for live-check failure.

## Risks / Trade-offs

- [12 nodes in 6 columns still dense] → Stack by lane as above; keep side branches to the two dashed returns; drop unused reverse edges before touching `via`.
- [Workflow `type` enum is not a Jira status kind] → Use `security` for gates (IN QA, LIVE CHECK), `backend` for active work, `external` for queues / CLOSED; do not invent types.
- [Teaching graph is not the full Jira machine] → Cards note that Jira also allows step-back and Any → CLOSED; those stay out of the default picture.
- [Showcase validation fails on the QA-fail or reopen corridor] → Follow Archify repair order (profile → overlap → through-node → crossings → labels); use `return-left` / `bottom-channel` only when the diagnostic names that subject.

## Migration Plan

Replace `docs/jira-ticket-lifecycle.html` in place. Keep the previous lifecycle JSON until `deliver` exits 0, then delete lifecycle sources. Rollback: restore the last delivered HTML and lifecycle JSON from git.

## Open Questions

None. Option A plus live-check-before-close is specified; remaining geometry is settled during apply against `validate --quality showcase`.
