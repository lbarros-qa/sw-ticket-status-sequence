## Context

The team's Jira workflow (screenshot 1) has 12 statuses with forward chain, several two-way pairs, a QA rejection return, a UAT review loop, and a reopen path. The live board (screenshot 2) uses different display column names (READY FOR DEV, UAT FEEDBACK, IN DEV, READY FOR QA, IN QA, IN UAT, READY FOR PUBLISH..., LIVE CHECK) and shows ticket conventions juniors must learn: `{blocked}` / `{waiting for other tickets to go live}` title prefixes, flags, due dates. The repo is greenfield except for the bundled `/archify` skill (validated locally: `node_modules` with `@fission-ai/openspec`; archify runs via `node .agents/skills/archify/bin/archify.mjs`).

## Goals / Non-Goals

**Goals:**
- One interactive HTML lifecycle diagram a junior can read top-to-bottom and interrogate edge-by-edge (trace, focus, search).
- Phase structure that mirrors how the team talks about the work: Intake → Development → QA → UAT/Deploy → Publish/Close.
- Explicit board-column bridge so "I see my ticket in IN UAT" maps to a workflow status.

**Non-Goals:**
- Changing the Jira workflow itself, or any automation.
- Modeling epics/sub-tasks, SLAs, or due-date rules beyond naming the conventions.
- Mobile-specific layout (desktop-first standalone artifact; narrow layouts may scroll vertically).

## Decisions

1. **Diagram type: archify `lifecycle`** (schema pins `schema_version: 1`). The core question juniors ask is a state-machine question ("what can happen from here?"), not a step/process question. `workflow` was rejected: it frames gates and actors, not status residence. Source: `schemas/lifecycle.schema.json`, `examples/deployment-release.lifecycle.json`.
2. **Semantic layout, not screenshot geometry.** Keep the meaning (statuses, directions, loops) but re-group: 5 phase columns (Intake / Development / QA / UAT & Deploy / Publish & Close) on the main rail, with paired active states (IN ANALYSIS, IN DEVELOPMENT, IN QA, IN UAT) plus UAT REVIEW grouped by phase. Lanes (max 4): a forward rail, a work-in-progress band, and a terminal band for CLOSED; final cell placement is settled during authoring against `validate --layout-json` diagnostics, not pre-planned coordinates.
3. **Loop transitions get labels; forward pairs stay sparse.** Rejection return labeled "QA rejected"; reopen labeled "Reopen"; the screenshot's "Ready for Publishing" badge is preserved verbatim as the label/edge note on the READY FOR DEPLOYMENT → ACCEPTANCE gate (it reads as a Jira transition property, not a status). The "Any" badge is treated as a global-transition hint, rendered as a `dashed`-variant note edge only if it survives validation cleanly; per-state `tag` carries the board column short name (e.g., "board: READY FOR DEV").
4. **Board mapping lives in two places:** per-state `tag` for the quick lookup, and closing cards for the full status↔column map plus board conventions (`{blocked}`, `{waiting for other tickets to go live}`, flags). Cards keep the diagram clean while making the mapping printable for onboarding docs.
5. **Guided views (3–5)** answering junior questions: "Publish path" (READY FOR DEPLOYMENT → ACCEPTANCE → CLOSED), "QA loop" (READY FOR QA / IN QA / rework), "UAT review" (UAT REVIEW / IN UAT / board UAT FEEDBACK), and optionally "Reopen path". One note each, ≤140 chars.
6. **Language: English** (`meta.locale: "en"`, matching the conversation); default classic visual preset (no `meta.visual_preset`), no subtitle, legend `auto`.
7. **Animation: `meta.animation: "trace"`** — juniors hover/click states to see connected flows animate, making "where can this ticket go next?" immediately visible without reading the full graph.
8. **Quality: `standard`** — showcase quality is unachievable with 21 transitions in the lifecycle renderer's fixed grid (5+ columns, fixed pitch). Standard quality passes validation with 0 composition errors; composition warnings are acceptable and do not block delivery.
9. **File placement:** candidate JSON at `docs/archify/jira-ticket-lifecycle.lifecycle.json`; delivered HTML at `docs/jira-ticket-lifecycle.html`.

## Assumptions from the screenshots (verify during apply, before authoring)

- Bidirectional arrow pairs (OPEN↔IN ANALYSIS, IN ANALYSIS↔READY FOR INTAKE, READY FOR INTAKE↔READY FOR DEVELOPMENT, READY FOR DEVELOPMENT↔IN DEVELOPMENT, IN DEVELOPMENT↔READY FOR QA, READY FOR QA↔IN QA, IN UAT↔READY FOR DEPLOYMENT) are real both-way transitions and will be modeled as two directed edges each, unless confirmed otherwise.
- The left-pointing arrow from IN QA returns to READY FOR DEVELOPMENT (QA rejected → back to dev).
- UAT REVIEW is entered from the QA side and exits into IN UAT.
- The reopen edge returns to OPEN; its exact origin is ambiguous in the screenshot. Decision (user, 2026-09-11): model as drawn from the ACCEPTANCE/CLOSED area and verify against Jira during apply before delivery.

## Risks / Trade-offs

- [12 states + rework loops → crowded geometry] → Use automatic routes, sparse labels, ≤3 closing cards; guided views reduce what must be read at once. Accept standard-quality warnings; focus on trace animation value over visual perfection.
- [Mis-modeled transition direction teaches juniors wrong] → Assumptions section above is a checklist; confirm each against the screenshot/Jira before the first candidate; final validation freezes the candidate.
- [State/cell collisions in a 5-column phase grid] → Use `validate --layout-json` compiler receipt; rely on lanes and yOffset before considering explicit via/channel controls.
- [Board display names drift from workflow names over time] → Mapping lives in data (tags/cards) and is cheap to re-author; note in cards that Jira column names may change.

## Migration Plan

Not applicable — additive documentation artifact. Rollback is deleting `docs/archify/` and `docs/jira-ticket-lifecycle.html`.

## Open Questions

- None blocking. The remaining uncertainty (reopen origin) is handled as a verification task during apply; two-way pairs confirmed as real both-direction transitions (user, 2026-09-11).
