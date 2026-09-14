## MODIFIED Requirements

### Requirement: The diagram models the full ticket status machine

The deliverable SHALL be a single self-contained HTML workflow diagram (archify `workflow` type, schema version 2) that renders all 12 workflow statuses: OPEN, IN ANALYSIS, READY FOR INTAKE, READY FOR DEVELOPMENT, IN DEVELOPMENT, READY FOR QA, IN QA, UAT REVIEW, IN UAT, READY FOR DEPLOYMENT, ACCEPTANCE, CLOSED.

#### Scenario: Viewer sees every status

- **WHEN** a QA junior or business reader opens the delivered HTML in a desktop browser
- **THEN** all 12 statuses are visible in one diagram, with the happy path (OPEN through CLOSED, including live check) readable as one obvious main sequence without overlapping connectors

### Requirement: Transitions show direction and meaning

The diagram SHALL render only the Option A teaching set of transitions, each with correct direction. Adjacent reverse edges from the Jira workflow screenshot SHALL NOT appear on the default view. Each transition whose meaning is not implied by its endpoints SHALL carry a label. The required set is:

- Happy path, in order: OPEN → IN ANALYSIS → READY FOR INTAKE → READY FOR DEVELOPMENT → IN DEVELOPMENT → READY FOR QA → IN QA → UAT REVIEW → IN UAT → READY FOR DEPLOYMENT → ACCEPTANCE → CLOSED
- QA fail: IN QA → READY FOR DEVELOPMENT, labeled so a reader can see the ticket returns to developers
- Reopen: CLOSED → OPEN, labeled Reopen
- Live check before production: ACCEPTANCE is the LIVE CHECK gate; the happy path SHALL NOT skip ACCEPTANCE on the way to CLOSED

The diagram SHALL NOT include a "Live check failed" return from ACCEPTANCE to READY FOR DEPLOYMENT.

#### Scenario: QA rejects a ticket

- **WHEN** the viewer traces edges touching IN QA
- **THEN** the traced paths show the forward move to UAT REVIEW and the rejection return to READY FOR DEVELOPMENT, and no other IN QA exits

#### Scenario: A closed ticket comes back

- **WHEN** the viewer traces edges touching OPEN
- **THEN** a labeled reopen path from CLOSED back to OPEN is visible

#### Scenario: Live check before production

- **WHEN** the viewer follows the happy path from READY FOR DEPLOYMENT toward CLOSED
- **THEN** the path goes through ACCEPTANCE (LIVE CHECK) before CLOSED, and no edge skips that gate

### Requirement: Per-status plain-language guidance

Each status SHALL carry a short description (sublabel) telling a QA junior or business reader what the ticket is waiting for in that state and who typically acts next (BA, developer, QA, business reviewer, or owner).

#### Scenario: Understanding IN ANALYSIS

- **WHEN** the viewer reads the IN ANALYSIS state
- **THEN** the sublabel states in plain language what happens there and who drives it

#### Scenario: Understanding ACCEPTANCE

- **WHEN** the viewer reads the ACCEPTANCE state
- **THEN** the sublabel states that the owner checks the live site before the ticket can close

### Requirement: Board columns map to workflow statuses

The diagram SHALL connect the board columns observed on the dashboard (READY FOR DEV, UAT FEEDBACK, IN DEV, READY FOR QA, IN QA, IN UAT, READY FOR PUBLISH..., LIVE CHECK) to their workflow statuses. Where a board name differs from the workflow status name, the node SHALL show the board name as the primary label (or an equally visible tag) so a reader looking at the dashboard can find the matching node. The diagram SHALL note the board conventions (`{blocked}` and `{waiting for other tickets to go live}` title prefixes, flag markers, due dates) and that OPEN, IN ANALYSIS, READY FOR INTAKE, and CLOSED stay off this board.

#### Scenario: Junior locates a board column in the workflow

- **WHEN** the viewer looks for the READY FOR PUBLISH... board column
- **THEN** the corresponding node (READY FOR DEPLOYMENT / READY FOR PUBLISH...) and the states around it (IN UAT before, ACCEPTANCE / LIVE CHECK after) are identifiable in the diagram

#### Scenario: Live check matches the board

- **WHEN** the viewer looks for the LIVE CHECK board column
- **THEN** it maps to ACCEPTANCE and sits on the happy path immediately before CLOSED

### Requirement: Guided walkthrough views

The diagram SHALL define at least three and at most five curated guided views (archify `meta.views`) for a QA junior or business reader, each focusing the relevant statuses with a one-line note. The views SHALL cover: the happy path including live check, the QA fail loop, and the reopen path.

#### Scenario: Following the publish path

- **WHEN** the viewer opens the guided view about publishing
- **THEN** READY FOR DEPLOYMENT, ACCEPTANCE (LIVE CHECK), and CLOSED are focused with a note that live check happens before production close

#### Scenario: Following the QA fail loop

- **WHEN** the viewer opens the guided view about QA
- **THEN** IN QA, READY FOR DEVELOPMENT, and the labeled fail return are focused

### Requirement: Flow animation for learning

The diagram SHALL enable trace animation (`meta.animation: "trace"`) so that when a reader activates a state or transition, the connected flows animate visually — showing exactly where a ticket can go next and where it came from on the Option A graph.

#### Scenario: Junior traces the QA loop

- **WHEN** the viewer activates the IN QA state in the trace view
- **THEN** the forward path to UAT REVIEW and the rejection return to READY FOR DEVELOPMENT both animate, making the two possible outcomes immediately visible

### Requirement: Validated single-file deliverable

The delivered HTML SHALL be produced from an archify workflow v2 candidate JSON validated at showcase quality (all artifact checks, 0 composition errors, 0 warnings), with trace animation enabled. Connectors SHALL not overlap nodes, share ambiguous corridors, or mask each other's labels. The file SHALL open standalone in a browser with no network access.

#### Scenario: Validation receipt

- **WHEN** archify `deliver` runs on the candidate JSON with `--quality showcase`
- **THEN** the command exits 0 with a receipt showing all artifact checks passed and 0 composition errors and 0 warnings, and the HTML file exists in `docs/`

#### Scenario: Offline viewing

- **WHEN** the HTML file is opened locally without network access
- **THEN** the diagram renders fully with theme switching, pan/zoom, and edge tracing functional

#### Scenario: No overlapping connectors

- **WHEN** a reader views the default diagram at desktop size
- **THEN** each Option A arrow is a distinct route whose label does not sit on another route or node
