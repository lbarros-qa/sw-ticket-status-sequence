## Purpose

Give junior engineers a single interactive status-machine diagram of the team's Jira ticket workflow — every status, every allowed transition, and how the board columns they see daily map onto those statuses — so they can answer "where can this ticket go next?" without asking a senior.

## ADDED Requirements

### Requirement: The diagram models the full ticket status machine

The deliverable SHALL be a single self-contained HTML lifecycle diagram (archify `lifecycle` type) that renders all 12 workflow statuses: OPEN, IN ANALYSIS, READY FOR INTAKE, READY FOR DEVELOPMENT, IN DEVELOPMENT, READY FOR QA, IN QA, UAT REVIEW, IN UAT, READY FOR DEPLOYMENT, ACCEPTANCE, CLOSED.

#### Scenario: Viewer sees every status

- **WHEN** a junior opens the delivered HTML in a desktop browser
- **THEN** all 12 statuses are visible in one diagram, with the forward path (OPEN through CLOSED) readable as one obvious main sequence

### Requirement: Transitions show direction and meaning

Every transition in the workflow SHALL be rendered with correct direction, and each transition whose meaning is not implied by its endpoints SHALL carry a label. At minimum the diagram SHALL include: the forward chain, the QA rejection return (IN QA back to READY FOR DEVELOPMENT), the UAT review loop (IN QA / IN UAT side via UAT REVIEW), the publish gate (READY FOR DEPLOYMENT into ACCEPTANCE), acceptance completion (ACCEPTANCE into CLOSED), and the reopen path back to OPEN.

#### Scenario: QA rejects a ticket

- **WHEN** the viewer traces edges touching IN QA
- **THEN** the traced paths show both the forward move to UAT/next stage and the rejection return to READY FOR DEVELOPMENT

#### Scenario: A closed ticket comes back

- **WHEN** the viewer traces edges touching OPEN
- **THEN** a labeled reopen path from ACCEPTANCE/CLOSED territory back to OPEN is visible

### Requirement: Per-status plain-language guidance

Each status SHALL carry a short description (sublabel) telling a junior what the ticket is waiting for in that state and who typically acts next (e.g., author/BA, developer, QA, product owner).

#### Scenario: Understanding IN ANALYSIS

- **WHEN** the viewer reads the IN ANALYSIS state
- **THEN** the sublabel states in plain language what happens there and who drives it

### Requirement: Board columns map to workflow statuses

The diagram SHALL connect the board columns observed on the dashboard (READY FOR DEV, UAT FEEDBACK, IN DEV, READY FOR QA, IN QA, IN UAT, READY FOR PUBLISH..., LIVE CHECK) to their workflow statuses, and SHALL note the board conventions juniors will see (`{blocked}` and `{waiting for other tickets to go live}` title prefixes, flag markers, due dates).

#### Scenario: Junior locates a board column in the workflow

- **WHEN** the viewer reads the mapping notes for the READY FOR PUBLISH... board column
- **THEN** the corresponding workflow status (READY FOR DEPLOYMENT) and the states around it are identifiable in the diagram

### Requirement: Guided walkthrough views

The diagram SHALL define at least three and at most five curated guided views (archify `meta.views`) answering common junior questions, each focusing the relevant statuses with a one-line note.

#### Scenario: Following the publish path

- **WHEN** the viewer opens the guided view about publishing
- **THEN** only the statuses on the publish path (READY FOR DEPLOYMENT, ACCEPTANCE, CLOSED and neighbors) are focused with an explanatory note

### Requirement: Flow animation for learning

The diagram SHALL enable trace animation (`meta.animation: "trace"`) so that when a junior activates a state or transition, the connected flows animate visually — showing exactly where a ticket can go next and where it came from.

#### Scenario: Junior traces the QA loop

- **WHEN** the viewer activates the IN QA state in the trace view
- **THEN** the forward path to UAT REVIEW and the rejection return to READY FOR DEVELOPMENT both animate, making the two possible outcomes immediately visible

### Requirement: Validated single-file deliverable

The delivered HTML SHALL be produced from an archify candidate JSON validated at standard quality (0 composition errors; warnings are acceptable), with trace animation enabled so transitions flow visually when the viewer activates them. The file SHALL open standalone in a browser with no network access.

#### Scenario: Validation receipt

- **WHEN** archify `deliver` runs on the candidate JSON
- **THEN** the command exits 0 with a receipt showing artifact checks passed, and the HTML file exists in `docs/`

#### Scenario: Offline viewing

- **WHEN** the HTML file is opened locally without network access
- **THEN** the diagram renders fully with theme switching, pan/zoom, and edge tracing functional
