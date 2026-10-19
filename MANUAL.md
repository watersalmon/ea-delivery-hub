# EA Delivery Hub User Manual

Screen-by-screen reference for **EA Delivery Hub** (version 0.1.0, Phase 5 plus the later editing, deletion and link-picker improvements). The interface is in English, so menu, button, column and message names are quoted exactly as they appear on screen, in `code format` or in **bold**.

If you are new to the app, start with the [tutorial](TUTORIAL.md), which walks through a complete programme from start to finish. This manual is for looking up what each screen does.

## Contents

1. [What it is and how to think about it](#1-what-it-is-and-how-to-think-about-it)
2. [Installation and start-up](#2-installation-and-start-up)
3. [The common interface](#3-the-common-interface)
4. [Key concepts: phases, gates, rules and RAG](#4-key-concepts-phases-gates-rules-and-rag)
5. [Main](#5-main): Home · Program dashboard · First 10 days
6. [Define](#6-define): Architecture Brief · Goals & Solution Dependency Map · Requirements & RTM · Architecture (HLD/LLD)
7. [Govern](#7-govern): Gates & Roadmap · Decisions (ADR) · Design Authority · RAID & Dependencies · Stakeholders, RACI & Comms
8. [Deliver & Close](#8-deliver--close): Operational Readiness · As-built & Handover · Lessons Learned
9. [Enablement](#9-enablement): Template Library · EA Playbook · Administration
10. [Exports (templates T01-T17)](#10-exports-templates-t01-t17)
11. [Governance rules catalogue](#11-governance-rules-catalogue)
12. [Known limitations and tips](#12-known-limitations-and-tips)

---

## 1. What it is and how to think about it

EA Delivery Hub is the "operating system" of the Enterprise/Delivery Architect during an engagement. It is not a document repository: it stores the programme data (requirements, decisions, risks, gates…) and, from that data, it:

- **Maintains the traceability chain** outcome → requirement → architecture element → decision (ADR) → test/evidence → gate.
- **Applies the quality rules** of the EA practical guide automatically (a single Accountable per activity, no critical risk without an owner, no orphan requirements…). Violations show up as alerts, badges and traffic lights, and can **block a gate**.
- **Generates the 17 delivery templates** (Excel, Word, PowerPoint) in Dell style, blank or filled with the programme data.

The mantra behind the design comes from the guide: *Outcome before product. Decision before document. Evidence before opinion. Governance before improvisation. Operability before closure.*

The app is local and single-user: there is no login. The name shown at the top right (`ea.demo` by default) comes from the `EA_USER` setting and is used as the author in records and in the audit log.

## 2. Installation and start-up

Installing in Docker inside WSL2, updates and backups are described in **[INSTALL.md](INSTALL.md)** and are not repeated here. In short: `bash scripts/install.sh --seed` installs the app with the demo programmes (`--port` changes the port if 8080 is taken) and `scripts/backup.sh` / `scripts/restore.sh` save and restore the data.

Once installed, open the app at **http://localhost:8080** from the Windows browser. It is only reachable from that machine.

Useful commands from the repo folder (in WSL):

| Command | What it does |
|---|---|
| `make up` | Builds and starts the app |
| `make down` | Stops it (data is kept in the volume) |
| `make seed` | Deletes and recreates **only** the three demo programmes; your own programmes are not touched |
| `make export-all` | Generates all templates, blank and filled, in `./exports` |
| `make logs` | Shows the app log |

If the database is empty you will see **"There is no program to show"**: load the demos with `make seed` or create a programme in **Administration**.

### The demo programmes

| Code | Programme | Phase | What it is for |
|---|---|---|---|
| `SPC-AASD` | Sovereign Private Cloud | 4 · Govern delivery | The most complete one. It deliberately triggers every rule, so it is red and G4 is blocked. Ideal for seeing alerts. |
| `HBM-ICDA` | Hybrid Backup Modernisation | 5 · Validate / Transition | A programme near closure: useful for ORR, handover and baselines. |
| `EDP-CMM` | Edge Data Platform Discovery | 1 · Discover | A freshly started programme with little data. |

All client and person names are fictitious.

## 3. The common interface

![Program dashboard with the top bar and sidebar](screenshots/fase5_dashboard.png)

### Top bar

From left to right:

- **EA Delivery Hub / Architecture & Delivery Governance**: click to go back to **Home**.
- **Active programme selector**: every screen (except Home, a blank Template Library, Playbook and Administration) works on the programme chosen here. The choice is remembered in the browser.
- **Search** `Search RK-, AC-, IS-, ADR-, REQ-…` (shortcut **Ctrl K**): searches by ID or title across RAID items, requirements, ADRs, dependencies, goals and objectives of the active programme, from 2 characters. **Escape** closes the results.
- **`+ Register`**: quick creation of a `Risk`, `Action`, `Issue`, `Decision / ADR`, `Change` or `Dependency`. Note: `Decision / ADR` creates a decision in the RAID *Decisions* tab (ID `DC-`), not a formal ADR; ADRs are created in **Decisions (ADR) → New ADR**.
- **Bell (Governance alerts)**: the red number is the total of rule alerts for the programme. Opening it shows the full list, each with a link to the item and a suggested action (`→ …`), plus a shortcut to **Weekly health**.
- **Current user** (`ea.demo` by default).

### Sidebar

It groups the screens by the guide's lifecycle: **Main**, **Define**, **Govern**, **Deliver & Close** and **Enablement**. Some entries carry a **badge calculated on the fly**, never fixed:

| Badge on… | What it counts |
|---|---|
| Requirements & RTM | Orphan requirements (rule RTM-01) |
| Gates & Roadmap | The active gate (red if blocked) |
| Decisions (ADR) | Pending ADRs (Proposed or Under Review) |
| RAID & Dependencies | Critical risks without an owner (RAID-01) |
| Stakeholders, RACI & Comms | RACI errors (RACI-01/02) |

### Work area

Each screen has breadcrumbs, the title, a row of **context chips** on the right (phase, gate, RAG, counters) and, on artefact screens, the **Export** button. Artefact screens (RAID, RTM, RACI, Goals, Dependencies…) start with three boxes, **Purpose / Process / Additional Information**, explaining what the artefact is for, as in the Dell templates.

The classification (`Internal Use - Confidential` by default) and the version appear at the foot of every screen.

### Spreadsheet-style tables

Most data is shown in tables with a blue header and alternating rows. They work the same way throughout the app:

- **Sort**: click the column name (toggles ascending/descending).
- **Filter**: click the funnel icon of a column; a row of filter boxes appears. It filters by contained text, in the browser.
- **Inline editing**: editable cells show the tooltip `Click to edit`. Click (or **Enter** on a focused cell) opens the editor: text, text area, drop-down, date or Yes/No. Every editor has **Save** and **Cancel** buttons: nothing is saved when you pick a value, you must press **Save** or **Enter** (in text areas, **Enter** saves and **Shift+Enter** inserts a line break). **Cancel** or **Escape** restore the cell without saving and without reloading the page. The server validates the value; if there is an error it is shown under the field and the editor stays open (nothing is saved). Required texts (titles, statements, names) cannot be left empty.

  ![Inline editor with Save and Cancel](screenshots/inline-editor-save-cancel.jpg)

- **After saving**: the screen refreshes by itself in the background (counters, panels and derived data) without a full reload, and it keeps the column filters, the sort order, expanded panels and the scroll position. The same happens when you tick a checklist item (Definition of Done, first 10 days, dashboard checklist).
- **Flagged rows**: a coloured bar on the left of a row (`flag-red` / `flag-amber`) means a governance rule affects that item. Hover the ID to see which rule.

### Picking links in a pop-up window

Wherever one record is linked to several others, the choice is made in a **pop-up window** (a modal dialog) instead of inside the cell, so long lists stay manageable:

- The window lists every candidate as a **checkbox**, with the current links already ticked. A **search box** at the top (focused when the window opens) filters the list as you type.
- **Save** (or **Enter**) sets the links to exactly what is ticked: ticked items are added, unticked ones are removed. The window closes and the screen refreshes softly, keeping filters, sort order and scroll. **Cancel**, **Escape** or the backdrop close it without saving anything.
- It is used in two places: the link cells of the **RTM** ([section 6.3](#63-requirements--rtm)) and the architecture elements of an **ADR** ([section 7.2](#72-decisions-adr)).

### Deleting records and removing links

Rows that can be deleted show a **trash icon** next to their ID. Clicking it opens a **confirmation page** (`Delete …`) that tells you exactly what will happen before anything is removed:

![Delete confirmation page](screenshots/manual_delete_confirm.png)

- **It cannot be deleted yet**: lists the records that depend on this one and must be resolved first (each with a link). Examples: a goal that still has objectives; an ADR that is on the agenda of a Design Authority session or that justifies a gate waiver; a closed Design Authority session (its minutes are the record of what was decided). The **Delete** button is not offered while there are blockers.
- **Relations that will be removed**: optional references that are cleared while the related records are kept: RTM and forum links, the workstream of risks/requirements/elements/work packages, the source risk of issues and escalations, `supersedes` between ADRs, work-package dependencies and the links of HLD/LLD reviews.
- **Also deleted with it**: the record's own detail: the updates of a risk, the options and history of an ADR, the checklist answers of a review, the RACI cells of a role or activity, the agenda of an unclosed session and the file of a diagram.
- Deleting a **programme** (in **Administration**) removes all of its records and requires typing its code to confirm.
- Not deletable: gates G0-G6 (they are the guide's lifecycle), baselines (approved snapshots used by BASE-01), the Architecture Brief, predefined checklists and settings. Workstreams are not deleted either, because risks, requirements and work packages usually reference them.
- Every deletion, and every reference that is cleared, is recorded in the **audit log** with the user and the date.

To remove a **relation without deleting anything**: in the RTM, open the link cell and untick the item; in an ADR, use **Edit links** or **unlink**; the source risk of issues and escalations and the workstream of any row can be changed or cleared with the inline editor.

### Export menu

![Export menu](screenshots/fase5_export_menu.png)

The **Export** button lists the templates of that screen. Each one has three links:

- **With program data**: the template filled with the active programme, in the programme's export language (EN by default).
- **ES**: the same, in Spanish.
- **Blank template**: the empty template, with 2-3 example rows in grey italics.

The file is downloaded as `{Programme}_{Artefact}_v{version}_{YYYYMMDD}.{ext}`.

## 4. Key concepts: phases, gates, rules and RAG

### Phases and gates

The programme moves through the guide's 7 phases. **G*n* is the exit gate of phase *n***:

| Phase | Name on screen | Gate | Gate key question | Exit criterion |
|---|---|---|---|---|
| 0 | Qualify / Presales | G0 | Is the solution deliverable? | Visible risks; testable assumptions; defined handover |
| 1 | Discover | G1 | Do we understand the real context? | Context and requirements baselined |
| 2 | Define target | G2 | Is the target architecture coherent? | HLD and key decisions accepted |
| 3 | Plan transition | G3 | Is there a viable path? | Incremental plan agreed |
| 4 | Govern delivery | G4 | Is what was approved being implemented? | Conformance or approved deviations |
| 5 | Validate / Transition | G5 | Does it work and can it be operated? | Acceptance and operational readiness |
| 6 | Close / Evolve | G6 | What was achieved and what is next? | Final architecture and backlog transferred |

The **active phase only changes when the active gate is passed** in **Gates & Roadmap** (it is not edited by hand in Administration). When G*n* is passed, the programme moves to phase *n+1* and the next gate becomes `In Progress`.

### Governance rules

An engine evaluates 17 rules on every page load (full catalogue in [section 11](#11-governance-rules-catalogue)). Each result has a **red** or **amber** severity, a message and a suggested action. They feed:

- the alert bell and the sidebar badges,
- the coloured bars on rows,
- the dashboard's **weekly health** (7 controls),
- **gate blocking**: a red rule associated with a gate prevents passing it, unless there is a *waiver*.

There is nothing to "run": fixing the data (setting an owner, linking a requirement…) makes the alert disappear.

### RAG

A single colour scheme across the whole app and in the exports: **green** (fine), **amber** (attention), **red** (blocking), **blue** (informative / in progress) and **grey** (not assessed).

The **Risk Score** is Probability × Impact. With the default **1-3 × 1-3** scale: green 1-2, amber 3-6, red ≥ 7. With **1-5 × 1-5**: green 1-4, amber 5-14, red ≥ 15. The thresholds are changed per programme in Administration.

### IDs

Sequential per programme and type, with 3 digits: `RK-001` (risk), `AC-` (action), `IS-` (issue), `DC-` (RAID decision), `ES-` (escalation), `CH-` (change), `CM-` (communication), `LL-` (lesson learned), `ADR-`, `REQ-` (functional), `NFR-` (non-functional), `Goal-`, `Obj-`, `OUT-` (outcome), `AE-` (architecture element), `WP-` (work package), `DEP-` (dependency).

---

## 5. Main

### 5.1 Home (Program Portfolio)

![Program Portfolio](screenshots/fase5_portfolio.png)

A view of all programmes.

- **KPIs**: `Programs`, `Programs in red`, `Critical risks without owner` (RAID-01 across the portfolio) and `Pending ADRs`.
- **One card per programme** with name, client, overall RAG, phase chips, active gate (`· blocked` if it has blockers) and the number of red alerts, the **Top risks** by score, `Pending ADRs` and `Next forum` (the next planned Design Authority session).
- **`Open dashboard`** selects that programme and opens its dashboard.
- **Comparison**: a table with the traffic light of each of the 7 controls per programme. `Sort by:` orders by `Health`, `Name`, `Phase` or `Red alerts`.

### 5.2 Program dashboard

See the screenshot in [section 3](#3-the-common-interface). It is the control screen of the active programme.

- **Chips**: phase, `Active gate` (with `· blocked`) and `Overall` (overall RAG = the worst of the 7 controls).
- **Export**: `T12 · Weekly Health Check` as XLSX and as PPTX (one slide).
- **KPIs**: `Overall health`, `Red alerts`, `Open risks` (links to the RAID), `Pending ADRs` (links to ADR) and `RTM coverage` (% of requirements traced end to end; green ≥ 90 %, amber ≥ 60 %).
- **Weekly health check** (guide §11.1): the 7 controls `Outcomes & scope`, `Decisions`, `Architecture`, `Dependencies`, `RAID`, `Requirements` and `Operations`. Each takes the colour of its worst open rule. Click `N findings · …` to expand the findings with a direct link to the item (red ones come expanded).
- **Am I really in charge?** (guide §11.2): an 8-question self-assessment checklist. Tick items with a click; it is saved per programme.
- **Gate progress**: G0-G6 with their status. A blocked active gate says `Blocked by N red rules`.
- **Risk matrix**: P × I matrix with the number of open risks per cell; clicking a cell opens the RAID filtered by that combination.
- **NFR compliance vs. target**: a radar with the % of compliance of each measurable NFR against its target (100 = target met). Only NFRs with a target value and an achieved value appear; both are filled in on the requirement's edit page ([section 6.3](#63-requirements--rtm)).
- **Blocking issues**: open issues, and below them **Red rules blocking G*n***, the rules that prevent passing the active gate.
- **Latest decisions**: the latest ADRs with status, level, critical path, owner and date.

### 5.3 First 10 days

![First 10 days](screenshots/fase5_first_10_days.png)

A wizard to start an engagement following guide §3. By day 10 you should be able to explain which problem is being solved, who decides, what is delivered, how the workstreams fit together, the main risks and the pending decisions.

- **Five steps**: `Before kick-off`, `Days 1-2`, `Days 3-5`, `Days 6-7`, `Days 8-10`. Navigate with the buttons in the top row or with `Previous` / `Next`. A step gets a ✓ (green border) when all its actions are ticked.
- In every step: **EA actions** (a checklist ticked with a click) and **Exit evidence** (what you should have at the end of that stretch), with shortcuts to the relevant screen: Architecture Brief, Dependency map, Decision backlog, Context diagram, Governance Charter (DOCX), Design Authority, RAID actions, or `Register a transition risk`. The progress counters update as soon as you tick an item.
- **Questions you must be able to answer** (guide §3.1): the 8 day-10 questions, as a checklist.
- **Result of the first 10 days**: a link to the Brief (with its word count), `Generate DOCX` of the Governance Charter and **`Create baseline vN`**.
- **Export**: `T15 · First 10 days · XLSX`.

**Baselines.** `Create baseline` saves a versioned snapshot (v1, v2…) of requirements, architecture, RAID and roadmap, with date, approver and a SHA-256 hash. From then on, changing a baselined requirement or architecture element without linking a Change or an ADR triggers rule **BASE-01**. Baselines are compared in **As-built & Handover**.

---

## 6. Define

### 6.1 Architecture Brief

![Architecture Brief](screenshots/fase5_brief.png)

The 1-2 page document that lets anyone understand the programme in ten minutes (guide §13.1).

- Nine fixed sections, each with a hint of what to write: `Business outcomes and metrics`, `Scope in`, `Scope out`, `Context and high-level diagram`, `Principles and constraints`, `Workstreams and dependencies`, `Top risks and assumptions`, `Open decisions and dates` and `Governance, stakeholders and next steps`. **Click a section's text to edit it.**
- The `N / 900 words` chip turns red above 900 words, and a warning says it will probably exceed two pages.
- **What the hub already knows**: a side panel with what the app already knows about the programme (outcomes, workstreams and dependencies, top risks, open decisions, forums and decision makers) so you can copy it into the Brief.
- **Export**: `T01 · Architecture Brief · DOCX`.

### 6.2 Goals & Solution Dependency Map

![Goals & Solution Dependency Map](screenshots/fase5_goals_sdm.png)

A replica of the *Solution Dependency Log* in the Dell templates: business goals broken down into objectives, with a two-level header (**Goals and Objectives** | **Requirements Completion**) and cells merged per goal.

- Columns: `Goal ID`, `Goal`, `Obj ID`, `Objective`, `Acceptance Criteria`, `Completion Status`, `Approver`. **Acceptance Criteria**, **Completion Status** (`Not Started`, `In Progress`, `Complete`, `At Risk`) and **Approver** are editable inline.
- Next to each objective, `N req.` shows how many requirements are linked and takes you to the RTM.
- Under the table: **`+ Goal`** (new goal) and **`+ Objective`** (choose the goal, type the objective, acceptance criterion and approver).
- The `X / Y objectives complete` chip counts the objectives in `Complete`.
- **Export**: `T03 · Solution Dependency Map · XLSX` and `T16-goals · Status slide · PPTX`. **Open RTM** takes you to the traceability matrix.

**Business outcomes.** Below the map there is an editable table of outcomes (ID `OUT-`): outcome, metric, baseline value, target value and target date, all editable inline. The **`+ Outcome`** form under the table creates new ones. Outcomes are linked to requirements from the RTM, exactly like objectives.

![Business outcomes](screenshots/manual_goals_outcomes.png)

### 6.3 Requirements & RTM

![Requirements & RTM](screenshots/fase5_rtm.png)

The continuous traceability matrix: **Outcome / Objective → Requirement → Architecture Element → ADR → Test / Evidence → Gate → Status**.

- **KPIs**: total requirements, % traced to outcome/objective, % traced to design, % with test/evidence and `Orphan requirements` (RTM-01; click to filter).
- **Filters**: `Orphans only`, `Gate` and `Workstream`. `Clear filters` removes them.
- **Add a requirement or evidence** (collapsible):
  - *Requirement*: type `FR` or `NFR`, statement, MoSCoW priority (`Must`, `Should`, `Could`, `Won't`), NFR category (`Availability`, `RTO/RPO`, `Performance`, `Capacity`, `Security`, `Retention`), **measurable criterion** (in practice mandatory for NFRs, otherwise RTM-03 fires) and source. It gets an ID `REQ-` or `NFR-`.
  - *Evidence*: title, kind (`Automated test`, `Manual test`, `Demo`, `Document`), result (`Pass`, `Fail`, `Pending`), link and the requirement it proves (it is linked automatically).
- **Table**: each row is a requirement. What is missing appears in red as `— missing`. Evidence has a green (Pass), red (Fail) or grey (Pending) dot. The **Status** column shows the rules that affect the requirement or `Traced end to end` / `Traced (evidence pending)`.
- **Edit and delete a requirement**: each row has an **Edit** link and a trash icon. **Edit** opens the requirement's edit page with every field: statement, priority, status, workstream, source and, for NFRs, category, measurable criterion, target value, achieved value and unit (the last two feed the NFR radar on the dashboard). Changing the type between FR and NFR assigns a new `REQ-`/`NFR-` code; the RTM links are kept because they are stored by id.

  ![Requirement edit page](screenshots/manual_requirement_edit.png)

- **Editable link cells**: click (or press `Enter` on the focused) **Outcome / Objective**, **Architecture Element**, **ADR**, **Test / Evidence** or **Gate** cell of a requirement. A **pop-up window** for *that* type opens (see [Picking links in a pop-up window](#picking-links-in-a-pop-up-window)): a checkbox list of every candidate (the Outcome / Objective window groups Objectives and Outcomes), a search box and the current links already ticked. **Save** (or `Enter`) sets the requirement's links of that type to exactly what is ticked, and the RTM refreshes keeping the filters (`Orphans only`, `Gate`, `Workstream`, column filters), the sort order and the scroll. **Cancel** or `Esc` close it without saving. Empty cells (`— missing`) show `+ link` on hover or focus so it is obvious they are clickable; the cells are keyboard accessible. The **ADR** column also shows ADRs inherited through the requirement's architecture elements; only ADRs linked *directly* to the requirement are editable, the inherited ones appear in the window as *Inherited via architecture elements (read-only)*.

  ![RTM link pop-up](screenshots/rtm-cell-link-editor.jpg)

- **Export**: `T05 · RTM · XLSX` and `T16-rtm · Status slide · PPTX`.

A requirement is **traced end to end** when it has an outcome/objective, an architecture element and evidence. A `Won't` requirement does not count as an orphan.

### 6.4 Architecture (HLD/LLD)

![Architecture (HLD/LLD)](screenshots/fase5_architecture.png)

Three blocks:

- **Design reviews** (guide §7.1 and §7.2): a list of reviews with type, document, version, workstream, progress (questions answered / total), number of deviations and status. The form below (`HLD`/`LLD`, document, version, workstream) and **`Start review`** create a review with the guide's checklist (7 HLD questions or 6 LLD questions).
- **Architecture elements**: the building blocks of the architecture (ID `AE-`), with view (`Context`, `Application`, `Data`, `Technology`, `Security`, `Operations`), workstream and HLD/LLD references. Name, view, workstream, HLD and LLD are editable inline. They are the ones later linked to requirements and ADRs (in the RTM and from the ADR page). `+ Add` creates one.
- **Diagrams by view**: upload of PNG, JPG, SVG or draw.io exports (max. 10 MB) per view. Images are previewed; click to open them.
- **Export**: `T10 · HLD / LLD Review Checklist · XLSX`.

**Review screen** (click a review's title):

- Status buttons in the header: `Approved`, `Changes Requested`, `In Review`.
- Per question: **Answer** (`Yes`, `No`, `Partial`, `N/A`), **Comment** and **Deviation** (Yes/No), editable inline. `No`/`Partial` answers mark the row amber; a deviation without follow-up, red.
- **Follow-up**: on questions with `No`, `Partial` or a deviation, **→ ADR** (creates a `Proposed` ADR at Architecture level with the review's context) and **→ Change** (creates a `CH-` Change in the RAID with Change Control decision `Pending`) appear. The row is linked to the created item.

---

## 7. Govern

### 7.1 Gates & Roadmap

![Gates & Roadmap](screenshots/fase5_gates_roadmap.png)

**Gates.** One card per gate G0-G6 with the phase name, the key question and the status (`Passed`, `In Progress`, `Not Started`). The active gate is expanded; the others expand with a click. Inside:

- **Exit criteria**: the gate's criteria with editable **Status** (`Open`, `Met`, `Not Met`), **Evidence required** and the criterion text itself. They are an aid for the conversation: the pass button does **not** check that they are `Met`.
- **Red rules blocking G*n***: the open red rules associated with that gate.
- **Waiver (approved ADR)**: if the gate has to be passed with accepted blockers, choose an **approved** ADR that documents the exception and press `Save`. The blockers are overridden and it shows `Waived by ADR-xxx`.
- **`Pass G*n*`** (active gate only): if there are blockers without a waiver, the app rejects the action and shows `G*n* is blocked by N red rule(s): …`. Otherwise it marks the gate `Passed` with date and approver and **advances the programme to the next phase**.

![Blocked gate](screenshots/fase5_gate_blocked.png)

Which rules block each gate (only when red):

| Rules | Gates they block |
|---|---|
| RAID-01, RAID-04, ADR-01 | All (G0-G6) |
| RACI-01 | G1, G2 |
| RTM-01 (`Must` requirement) | G1, G2, G5 |
| DEP-01 (critical-path dependency) | G3, G4 |
| RTM-02, OPS-01, ORR-01 | G5 |

**Architecture roadmap.** A monthly Gantt chart with the work packages (bars coloured by status: blue `In Progress`, green `Done`, red `At Risk`), milestones as diamonds, the dashed red line for today and the *transition state* on the right. Below it, the editable table: name, workstream, start, end, milestone, transition state, depends on, gate and status (`Planned`, `In Progress`, `Done`, `At Risk`). The form underneath creates work packages (if you give no end date, the start date is used).

**Export**: `T11 · Architecture Roadmap · XLSX`.

### 7.2 Decisions (ADR)

![Decision log](screenshots/fase5_adr_list.png)

**Catalogue (Decision log).** Filters by `Status` (with a count per status), `Escalation level` and `Critical path only`. The **Alerts** column shows rules ADR-01/02/03 for each decision. **Export**: `T08-log · Decision Log · XLSX`. **`+ New ADR`** opens the form.

**Propose a decision (New ADR).** Fields: decision in one sentence (required), context (required), **Escalation level** (guide §7.3, with the forum that decides at each level), `On the critical path`, owner, due date and two initial options. If you tick critical path, owner and date are required. It is created in `Proposed` status.

| Level | When | Forum |
|---|---|---|
| Workstream | A configurable, reversible choice with no cross-cutting impact | Tech Lead / design sync |
| Architecture | Affects several domains, NFRs, a standard or the roadmap | Design Authority |
| Program | Affects scope, cost, schedule, resources or acceptance | PM / Change Control |
| Executive | Changes an outcome, a material exposure or a contractual commitment | SteerCo / sponsor |

**ADR page** (click the ID):

![ADR page](screenshots/fase5_adr_detail.png)

- At the top, the alerts affecting the ADR with their suggested action, and the **`ADR · DOCX`** button (template T08 for that decision).
- **Architecture Decision Record** (guide §13.2): title, context, decision, conditions, rationale, consequences, impacted artefacts and workstreams, impact on cost/schedule/operations, level, critical path, owner, date and approvers. **Click a field to edit it.**
- **Lifecycle**: `Proposed → Under Review → Approved | Rejected | Deferred → Superseded`. Only valid transitions are offered, with an optional note. Rules the app applies:
  - To approve, the ADR must first go through `Under Review`.
  - A critical-path decision cannot be approved without an owner and a date.
  - `Deferred` can go back to `Proposed` or `Under Review`.
  - To mark an ADR as `Superseded`, first state on the new ADR, with **Supersedes**, which one it replaces.
- **Status history**: every status change with date and note.
- **Traceability**: linked architecture elements and requirements. **Edit links** (next to *Architecture elements*) opens the same pop-up window as the RTM, with every architecture element as a checkbox, a search box and the current ones ticked; **Save** sets the ADR's architecture elements to exactly the ticked set. Each linked element also has an **unlink** button. The requirements listed are derived from those elements.
- **Options and trade-offs**: a matrix of options against the 7 Design Authority criteria (`Alignment`, `Security`, `Operability`, `Cost`, `Schedule`, `Reversibility`, `Compliance`), scored from 1 (bad) to 5 (excellent; for *Reversibility*, 5 = easy to undo). The highest total is shown in green. **Choose** marks the chosen option. Below, the options table with editable pros, cons, cost and risk, and `+ Add` to add real alternatives.

### 7.3 Design Authority

![Design Authority](screenshots/fase5_design_authority.png)

**Preparation (Architecture Design Authority).**

- **Prepare the next session**: date (by default, one week ahead) and the list of candidate ADRs (in `Proposed`, `Under Review` or `Deferred`), all ticked by default. **`Create session with selected decisions`** creates the session; included `Proposed` or `Deferred` ADRs move to `Under Review`.
- **Risks and dependencies needing attention** (agenda block 2) and **Open actions from previous sessions** (block 1), calculated automatically.
- **Minimum viable Design Authority**: input, participants, criteria, output and SLA according to guide §5.2.
- **Sessions**: list of sessions with status `Planned`, `In Progress` or `Closed`. An unclosed session can be deleted (trash icon); a closed one cannot, because its minutes are the record of what was decided.
- **Export**: `T02 · Governance Charter · DOCX`.

**Session** (click a date):

![Meeting mode](screenshots/fase5_design_authority_meeting_mode.png)

1. **`Start meeting mode`** moves the session to `In Progress`.
2. A 60-minute **agenda** in five blocks (5-10-30-10-5) with a **timer**: `Start`/`Pause` and `Next block`. The clock turns amber in the last minute and red (negative) when you overrun.
3. **Quorum**: tick who is present (`Mark present`). There is quorum if the EA, at least one Tech/Domain Lead and the client are present.
4. **Decisions, one by one**: for each ADR, edit the **Outcome** (`Approved`, `Rejected`, `Deferred`), conditions, owner, date and notes.
5. **Actions raised**: `+ Add` creates actions in the RAID (`AC-`) linked to the session.
6. **`Close session`**: requires an outcome on every decision. On closing it:
   - applies the result to each ADR (changes its status and copies owner, date and conditions);
   - automatically creates RAID actions: apply the conditions of an approved-with-conditions ADR, update the artefacts of an approved ADR with no impacted artefacts, or prepare the re-submission of a deferred one;
   - works out whether there was quorum and records the attendees.

The **`Agenda · DOCX`** button (before closing) or **`Minutes · DOCX`** (after) generates template T09.

### 7.4 RAID & Dependencies

![RAID Log, Risks tab](screenshots/fase5_raid_risks.png)

The RAID Log with the structure of Dell's Excel workbook.

- **Tabs**: `Cover`, `Risks`, `Actions`, `Issues`, `Decisions`, `Escalation`, `Changes`, `Comms`, `Lessons Learned`. In brackets, the open items (those not in `Closed`).
- **Cover**: programme data and the count of items and open items per tab.
- **Risks**:
  - **Probability × Impact**: an interactive matrix; clicking a cell filters the table (the `P × I ✕` chip removes the filter).
  - **Risk alerts**: the risk alerts (the first 5).
  - **Show cause / event / consequence / trigger / forum** checkbox: shows the collapsible columns.
  - Columns: `ID`, `Organization`, `Date Added`, `Reported By`, `Risk Title`, `Workstream`, `Risk Mitigation Owner`, `Probability`, `Impact`, `Risk Score` (coloured dot + number), `Due Date`, `Monitor Status`, `Contingency Plan`, `Risk Mitigation & Updates` and, collapsible, `Cause`, `Event`, `Consequence`, `Escalation Trigger`, `Escalation Forum` and `Trigger Met`.
  - **Risk Mitigation & Updates**: shows the **response** and the dated log (`10/14: …`). Click the response line (`Response: …`, or `none yet · click to add`) to edit it inline; if RAID-01 requires it and it is missing, the line shows in red. Type in `Add a dated update` and press `Add` to append a line with today's date.
- **Monitor Status** (all tabs): `Open`, `Action Required`, `In Progress`, `Monitoring`, `Closed`. Only `Closed` counts as closed for the rules.
- Columns of the other tabs: *Actions* (action, workstream, owner, date, status, notes), *Issues* (severity `Low`-`Critical`, source risk, resolution), *Decisions*, *Escalation* (source risk, forum), *Changes* (impact on scope, cost and schedule, `Change Control` `Pending`/`Approved`/`Rejected`), *Comms* (audience and notes) and *Lessons Learned* (context, decision, outcome, recommendation). The **source risk** of issues and escalations, and the **workstream** of any item, are editable (or can be left empty).
- **`+ New …`** (top right) creates an item of the current tab's type.
- **Export**: `T04 · RAID Log · XLSX` and `T16-raid · Status slide · PPTX`.

![Collapsible columns and matrix filter](screenshots/fase5_raid_filtered_extra_columns.png)

**Raising an escalation (RAID-04).** When a risk meets its escalation trigger and has no Escalation, the rule RAID-04 fires. In the risk's row (and in the alert) a **`Raise escalation to <forum>`** button appears: it creates an Escalation (`ES-`) linked to the risk, with its owner, workstream and escalation forum, due in 7 days; if one already exists it takes you to it. There are two other ways to clear the alert: set **Trigger Met** to No (collapsible column) or register the Escalation by hand choosing the source risk.

![Raise escalation](screenshots/manual_raid_escalation.png)

**Registering a risk (Register risk).** Required fields: title, **cause, event and consequence**. While you type, the *Risk statement (guide §8)* box composes the sentence: *"Due to [cause], there is a risk of [event], which could lead to [impact]."* In addition: probability and impact (according to the programme's scale), owner, response/mitigation, contingency plan, escalation trigger, escalation forum, date and workstream. The other types have shorter forms.

**Dependencies** (the `Dependencies` button or `/raid/dependencies`):

![Dependencies](screenshots/fase5_dependencies.png)

- **Dependency graph**: solid boxes = workstreams; dashed boxes = third parties or the client. Red arrow = no commitment and on the critical path; amber = no commitment; green = committed or internal.
- Editable table: dependency, `Needed by`, `Depends on`, type (`Delivery`, `Information`, `Decision`, `Resource`), `External`, `Commitment verified`, evidence, `Need date`, `Critical path`, status (`Open`, `At Risk`, `Closed`) and owner.
- **`+ New dependency`**. An **external** dependency without a verified commitment triggers DEP-01 (red if on the critical path). It is resolved by ticking `Commitment verified` and noting the evidence (PO, e-mail, signed plan).

### 7.5 Stakeholders, RACI & Comms

Four tabs. The chips show stakeholders, decision makers and detractors. If no decision maker or no detractor is identified, the STK-01 alert appears.

- **Directory**: name, organisation (`Customer`, `Dell`, `Partner`), role, influence (1-5), interest (1-5), stance (`Sponsor`, `Neutral`, `Detractor`) and `Decision maker`, all editable. Creation form below (`Decides` marks the decision maker).
- **Power / interest map**: quadrants `Keep satisfied`, `Manage closely`, `Monitor`, `Keep informed`. Green = sponsor, blue = neutral, red = detractor; big dots are decision makers.

![Power / interest map](screenshots/fase5_power_interest.png)

- **RACI**: activities × roles, preloaded with the guide §9 table adjusted to a single Accountable (PDR-001). Click a cell (or Enter) to change the value: `R`, `A`, `C`, `I`, `A/R`, `A/C`, with an optional qualifier. The **Live check (RACI-01 / RACI-02)** column validates instantly: exactly one A per activity and at least one R. `Add activity` and `Add role` extend the matrix.

![RACI](screenshots/fase5_raci.png)

- **Comms plan**: per stakeholder, key message, channel, cadence and communication owner.
- **Export**: `T06 · RACI Matrix · XLSX` and `T16-raci · Status slide · PPTX`. The stakeholder and communication plan (T07) is downloaded from **Template Library**.

---

## 8. Deliver & Close

### 8.1 Operational Readiness

![Operational Readiness Review](screenshots/fase5_orr.png)

Checks that the solution can be operated (guide §10).

- `G5 blocked` / `G5 not blocked` chip.
- **KPIs** by colour: `Green` (ready), `Amber` (late or partial), `Red` (blocks G5), `Not assessed` (no evidence).
- **Operations owner**: two editable fields, the name of the client's operations owner and whether operations is already `Involved`. If either is missing from phase 3 onwards, **OPS-01** fires, which blocks G5.
- **Checks table**: dimension, criterion, evidence, status (`green`, `amber`, `red`, `grey`) and owner, all editable. The guide's 7 dimensions are `Monitoring`, `Backup`, `Support`, `Access`, `Capacity`, `Runbooks` and `Skills`; any others are marked *program-specific*.
- **`+ Add check`**: dimension (with suggestions such as `DR / RTO-RPO`, `Patching`, `Observability`, `Security`), criterion and owner.
- Any check in `red` triggers **ORR-01** and blocks G5.
- **Export**: `T13 · Operational Readiness Review · XLSX`.

### 8.2 As-built & Handover

![As-built & Handover](screenshots/fase5_handover.png)

- **Baseline vs implemented**: compares a baseline (`From`) with another one or with the current state (`To` = `Current (as implemented)`) and press `Compare`. For each section (requirements, architecture, RAID, roadmap) it shows additions (`+`), removals (`−`) and changes (`~`, expandable field by field). If there are no baselines, it links to First 10 days to create one.
- **Technical debt, deviations and transfers**: items of type `TechDebt`, `Deviation` or `Transfer` with impact, estimated cost, receiving owner and review date. Creation form below.
- **Open decisions and risks to close or transfer**: open ADRs and risks/issues that must be closed or handed over.
- **Approved deviations (changes)**: the RAID Changes with their Change Control decision.
- **EA Definition of Done** (Annex A): an 8-point EA closure checklist; the chip counts the ticked ones and updates as you tick.
- **Export**: `T14 · As-built & Handover + EA DoD · DOCX`.

### 8.3 Lessons Learned

![Lessons Learned](screenshots/fase5_lessons.png)

- **Lessons of this program**: lesson, context, decision, outcome, recommendation and owner, all editable (they are the `LL-` items of the RAID). Creation form below.
- **From other programs**: lessons from the other programmes, with a search box. **Reuse** copies a lesson into this programme as a starting point, noting its origin.
- **Export**: `T17 · Lessons Learned · XLSX`.

---

## 9. Enablement

### 9.1 Template Library

![Template Library](screenshots/fase5_template_library.png)

A catalogue of the 17 templates. Filters by **Phase**, **Forum** and **Format** (`XLSX`, `DOCX`, `PPTX`). Each card shows number, name, formats, purpose, phases, forum, guide section and content. Buttons per file:

- **Download blank**: the empty template with example rows.
- **Generate with program data**: filled with the active programme.
- **Open the screen**: the app screen where that data is maintained.

Per-item templates (T08 ADR, T09 DA minutes) are best generated from their own page (`ADR · DOCX` or `Minutes · DOCX` button), where the chosen item is already selected.

### 9.2 EA Playbook

![EA Playbook](screenshots/fase5_playbook.png)

The EA guide, navigable, in four chapters: *The Enterprise Architect's role*, *Lifecycle by phase*, *Cadence, governance and escalation* and *Checklists, 30-60-90 and common mistakes*. On the left, the chapter index. The content is Markdown in `app/content/playbook/` and can be edited without touching code.

### 9.3 Administration

![Administration](screenshots/fase5_admin.png)

- **Programs**: a table of all programmes. Name, client, sponsor, PM, EA, classification, export language (`en`/`es`) and applicable regulatory frameworks are editable. The phase is shown but not edited (it changes when gates are passed). **Export** downloads the complete programme as JSON. The trash icon deletes the whole programme (type its code to confirm; the audit log is kept).
- **Create a programme**: code (letters, digits and hyphens, unique), name, client, sponsor, PM, EA, initial phase and risk scale; **`+ Create`**. The programme is born with default settings, gates G0-G6 (those before the chosen phase already passed) and the guide's RACI. It takes you to its dashboard.
- **Workstreams** (for the active programme): name, Tech / Domain Lead and scope, editable inline, with a **`+ Workstream`** form to add more. Workstreams feed the workstream drop-downs across the app. They cannot be deleted, because risks, requirements and work packages usually reference them.

  ![Workstreams](screenshots/manual_admin_workstreams.png)

- **Risk scale, RAG thresholds and decision SLA**: per programme, scale `1-3 × 1-3` or `1-5 × 1-5` (when you change it the thresholds go back to that scale's defaults), `Amber from score`, `Red from score` and `Decision SLA (days)` (10 by default; used by ADR-02).
- **Branding**: classification footer text, methodology banner and default export language. Colours and fonts live in `branding.yaml` and `tokens.css`; a logo is only used if you place one in `branding/`.
- **Import a program**: upload a JSON exported by the app (max. 20 MB), optionally with a different name. It always creates a **new programme** with new ids; if the code already exists it appends a `-2` suffix. Uploaded diagrams do not travel in the JSON.

---

## 10. Exports (templates T01-T17)

| # | Template | Format | Where it is generated |
|---|---|---|---|
| T01 | Architecture Brief | DOCX | Architecture Brief |
| T02 | Architecture Governance Charter | DOCX | Design Authority, First 10 days |
| T03 | Goals & Solution Dependency Map | XLSX | Goals & SDM |
| T04 | RAID Log | XLSX | RAID |
| T05 | Requirements Traceability Matrix | XLSX | Requirements & RTM |
| T06 | RACI Matrix | XLSX | Stakeholders → RACI |
| T07 | Stakeholder Map & Comms Plan | XLSX | Template Library |
| T08 | ADR (one per decision) + Decision Log | DOCX + XLSX | ADR page / ADR catalogue |
| T09 | Design Authority: agenda and minutes | DOCX | Design Authority session |
| T10 | HLD / LLD Review Checklist | XLSX | Architecture |
| T11 | Architecture Roadmap | XLSX | Gates & Roadmap |
| T12 | Weekly Health Check | XLSX + PPTX | Program dashboard |
| T13 | Operational Readiness Review | XLSX | Operational Readiness |
| T14 | As-built & Handover + EA Definition of Done | DOCX | As-built & Handover |
| T15 | First 10 days / Onboarding | XLSX | First 10 days |
| T16 | Status slide (RAID, Goals, RTM, RACI) | PPTX | Each screen, `Status slide` |
| T17 | Lessons Learned | XLSX | Lessons Learned |

All of them carry a cover sheet (programme, client, version, date, owner, status, classification and version control), a Dell-blue header, a black methodology banner and a classification footer. The Excel files come with frozen panes, autofilter, validation lists on closed-value fields, live formulas (score = P × I, coverage %, count of A in the RACI) and RAG conditional formatting.

## 11. Governance rules catalogue

| ID | What it detects | Severity | Control | How to fix it |
|---|---|---|---|---|
| RACI-01 | Activity without exactly one Accountable (`A`, `A/R` or `A/C`) | Red | Decisions | Leave a single A in the row |
| RACI-02 | Activity without any Responsible (`R` or `A/R`) | Amber | Decisions | Assign an R |
| RAID-01 | Open risk with a red score and no owner or no response | Red | RAID | Fill in `Risk Mitigation Owner` and the response |
| RAID-02 | Risk without cause, event, consequence, trigger or date | Amber | RAID | Complete the fields (collapsible columns) |
| RAID-03 | RAID item past its date and not closed | Amber | RAID | Update the status, re-plan the date or close it |
| RAID-04 | Risk whose escalation trigger is met and that has no Escalation | Red | RAID | Press **Raise escalation**, set **Trigger Met** to No, or close the risk |
| DEP-01 | External dependency without a verifiable commitment | Amber (red on the critical path) | Dependencies | Tick `Commitment verified` and note the evidence |
| ADR-01 | Open critical-path ADR without owner or without date | Red | Decisions | Assign an owner and a date |
| ADR-02 | ADR in Proposed/Under Review for more days than the SLA | Amber | Decisions | Take it to the next DA or escalate a level |
| ADR-03 | Approved ADR without impacted artefacts | Amber | Decisions | Fill in `Impacted artifacts` |
| RTM-01 | Requirement without outcome/objective or without architecture element | Red if `Must`, amber otherwise | Requirements | Link it in the RTM or descope it through Change Control |
| RTM-02 | Requirement without test/evidence (from phase 5) | Red | Requirements | Add linked evidence |
| RTM-03 | NFR without a measurable criterion | Amber | Requirements | Write metric and target (e.g. "≥ 99.95 % monthly") |
| STK-01 | Comms plan without decision makers or without detractors | Amber | Outcomes & scope | Mark decision makers and identify detractors |
| OPS-01 | No operations owner or no involvement (from phase 3) | Red | Operations | Fill in Operations owner and `Involved` in ORR |
| BASE-01 | Requirement or element changed since the baseline with no Change/ADR linked | Amber | Architecture | Link a Change or ADR, or revert the change |
| ORR-01 | Readiness check in red | Red | Operations | Recovery plan; move it to amber or green |

## 12. Known limitations and tips

- **Gate exit criteria are not checked** when passing a gate; only red rules count.
- **Some things are intentionally not deletable**: gates, baselines, the Architecture Brief, predefined checklists, settings and workstreams (see [Deleting records and removing links](#deleting-records-and-removing-links)). To "retire" an item that cannot be deleted, close it (`Closed`), reject it (ADR) or mark it `Won't` (requirement).
- **Programme import always creates a new programme**; it does not merge into an existing one, and uploaded diagrams are not part of the JSON.
- **Only ADRs linked directly to a requirement can be edited in the RTM ADR window.** ADRs inherited through its architecture elements change when you change which elements the ADR is linked to (**Edit links** on the ADR page).
- The note "Version control and export to Excel arrive in Phase 3" on the RAID *Cover* tab is out of date: the T04 export already works.
- To practise without fear, play with the demos and put them back as they were with `make seed`.
