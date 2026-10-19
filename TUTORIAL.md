# EA Delivery Hub Tutorial: a programme from start to finish

This tutorial teaches you to use EA Delivery Hub by doing. It has two parts:

- **Part A (15 minutes)**: a walk through the *Sovereign Private Cloud* demo programme to understand how alerts, traffic lights and gates look.
- **Part B (60-90 minutes)**: you create a new programme and take it from discovery to closure, going through all the screens.

Button, column and menu names are quoted exactly as they appear on screen. For the detail of each screen, see the [manual](MANUAL.md).

## Before you start

1. Install the app following [INSTALL.md](INSTALL.md), with the demo programmes: `bash scripts/install.sh --seed`.
2. Open **http://localhost:8080** in the Windows browser.
3. You should see **Program Portfolio** with three programmes. If you see "There is no program to show", the demos are missing: run `make seed` (or reinstall with `--seed`).

> **Tip**: you can break the demos without fear. `make seed` leaves them as new and does not touch the programmes you created.

Two behaviours you will meet everywhere, so it helps to know them up front:

- **Editing is explicit.** Clicking a cell opens an editor with **Save** and **Cancel**. Nothing is saved until you press **Save** or **Enter**; **Cancel** or **Escape** put the cell back untouched. After saving, the screen refreshes itself and keeps your filters, sort order and scroll position.
- **Linking uses a pop-up window.** Wherever you link a record to several others (in the RTM and on an ADR), a window opens with a search box and a checkbox list. You will use it in B5.

---

## Part A · A walk through a red programme

### A1. The portfolio

On **Home** you will see one card per programme. *Sovereign Private Cloud* appears in red, in phase 4, with the chip `Gate G4 · blocked`. Scroll down to **Comparison**: each column (Outcomes, Decisions, Arch., Deps., RAID, Reqs., Ops) is one of the 7 weekly-health controls. Try `Sort by: Health`.

Press **Open dashboard** on the *Sovereign Private Cloud* card.

### A2. Reading the dashboard

- At the top: `Overall RED` and `Active gate G4 · blocked`.
- **Weekly health check**: the red controls come expanded. Each line says which rule fires (for example `RAID-01 · RK-004 Critical risk (score 9) without owner and response`) and the code is a link to the item.
- **Gate progress**: G4 says `Blocked by N red rules`.
- **Red rules blocking G4** (bottom right): the exact list of what prevents progress, with the suggested action (`→ …`).
- The **bell** in the top bar holds the same alerts, reachable from any screen.

### A3. Fixing an alert

Let's remove the blocker `DEP-01 · DEP-001` (a dependency on a telecoms operator, on the critical path, with no verifiable commitment).

1. In **Red rules blocking G4**, click the `DEP-001` link. **Dependencies** opens, with the red arrow in the graph.
2. In the DEP-001 row, click the **Commitment verified** cell and choose `Yes`, then press **Save**.
3. Click the **Evidence** cell, type "PO 4500123, signed 10/10" and press **Enter** (or the **Save** button; **Cancel** or Escape discard the change).
4. Go back to the dashboard: `DEP-01 · DEP-001` no longer appears and the **Dependencies** control has improved.

Now look at `RAID-01 · RK-004`: open the link and you will see the risk's row marked red in **RAID & Dependencies → Risks**. Click the **Risk Mitigation Owner** cell, type a name and press **Enter**. The alert changes from "without owner and response" to "without response": the owner is now there. Then click the `Response:` line in the **Risk Mitigation & Updates** column (it says `none yet · click to add`), write a response such as "Order from an alternative supplier" and press **Save**. The RAID-01 alert disappears.

### A4. Seeing a blocked gate

Open **Gates & Roadmap**. G4's card is expanded and red. Press **Pass G4**: the app rejects it with a message listing the pending rules. That is how the control works: a gate cannot be passed with open red rules, except with a *waiver* recorded as an approved ADR.

### A5. Chasing a link in the RTM

Open **Define → Requirements & RTM** and tick **Orphans only**. Each orphan shows `— missing` in red. Click the **Architecture Element** cell of an orphan: a window opens with every architecture element as a checkbox. Type a few letters in the search box to filter, tick one element and press **Save**. The window closes and the requirement leaves the orphan list; the **Orphan requirements** KPI and the sidebar badge go down by one, and **Orphans only** stays on. (To undo, open the cell again and untick it.)

When you finish, if you want to leave the demo as it was: `make seed`.

---

## Part B · Your own programme, from start to finish

You are going to manage a fictitious programme:

| Field | Value |
|---|---|
| Code | `HCM-NDC` |
| Name | Hybrid Cloud Migration |
| Client | Naviera del Cantábrico (fictitious) |
| Sponsor | Lucía Mendaro (CIO) |
| PM | Pablo Ortiz |
| EA | your name |

Use these data or your own. Dates are indicative: use dates close to today.

### B1. Create the programme (Administration)

1. Sidebar → **Enablement → Administration**.
2. In the form under the **Programs** table: `CODE` = `HCM-NDC`, name, client, sponsor, PM, EA, **Phase 1** and scale **1-3**.
3. Press **`+ Create`**. The app takes you to the new programme's dashboard, already selected at the top.

What it comes with: default settings (scale 1-3, amber from 3, red from 7, decision SLA of 10 days), gates G0-G6 with G0 already passed and G1 `In Progress`, and the guide's RACI matrix.

Look at the empty dashboard: it is probably amber because of **STK-01** (no decision makers or detractors). That is normal: you fix it in step B3.

**Workstreams.** Back in **Administration**, scroll to the **Workstreams** block (it works on the active programme). Add `Compute & Storage`, `Networking` and `Operations` with the **`+ Workstream`** form, giving each a Tech / Domain Lead. Click any field to edit it later. These names now fill the *Workstream* drop-downs in risks, requirements and work packages.

### B2. First 10 days

Sidebar → **Main → First 10 days**. It is the script for your first two weeks.

1. In **Step 1 · Before kick-off**, tick the EA actions you have done (click each one; the counters update immediately). In **Exit evidence** you have the **Register a transition risk** button: you will use it in B6.
2. Move through the steps with **Next**. Each one states the exit evidence and links to the screen where it is produced. You will come back here at the end of the definition part to create the baseline.
3. Also tick the **Questions you must be able to answer** as you become able to answer them.

### B3. Stakeholders, RACI and communication

Sidebar → **Govern → Stakeholders, RACI & Comms**.

1. **Directory** tab. Add, with the form below:

   | Name | Organization | Role | Influence | Interest | Stance | Decides |
   |---|---|---|---|---|---|---|
   | Lucía Mendaro | Customer | CIO | 5 | 4 | Sponsor | ✓ |
   | Andrés Peral | Customer | Head of Operations | 4 | 2 | Detractor | |
   | Marta Gil | Customer | Security Officer | 4 | 4 | Neutral | ✓ |
   | Pablo Ortiz | Dell | Project Manager | 3 | 5 | Sponsor | |

   With one decision maker and one detractor identified, the **STK-01** alert disappears. A typo in a name? Click the name to edit it.
2. **Power / interest map** tab: check that the high-influence detractor (red) falls in *Keep satisfied*. This is someone to manage.
3. **RACI** tab: the matrix comes preloaded and green (`OK`). Try it: click the *Outcomes and end-to-end architecture* / *Customer* cell and change it to `A`. The **Live check** column flags **RACI-01** (two Accountables) and the sidebar badge turns red. Put it back to `C`.
4. **Comms plan** tab: for Andrés Peral write a **Key message** ("Operations takes part in the design from day 1"), **Channel** ("1:1 meeting"), **Cadence** ("Fortnightly") and **Owner**.

### B4. Architecture Brief

Sidebar → **Define → Architecture Brief**. Click each section to write it. Example:

- **Business outcomes and metrics**: "Close the Bilbao data centre before December 2027; cut infrastructure cost by 30 %."
- **Scope in**: "Migration of 120 VMs to private cloud; immutable backup; DR between two sites."
- **Scope out**: "Application modernisation; end-user computing."
- **Open decisions and dates**: "Key management model, before 30/11."

Use the **What the hub already knows** panel to avoid retyping what the app already knows. Watch the `N / 900 words` chip: if it turns red, the Brief can no longer be read in ten minutes.

Try **Export → T01 · Architecture Brief · DOCX → With program data** and open the Word file.

### B5. Goals, requirements and architecture (traceability)

This is the heart of the app: every requirement can be followed from the business goal to the evidence.

**Goals and objectives.** Sidebar → **Define → Goals & Solution Dependency Map**.

1. In `New goal…` type "Infrastructure as a service" and press **`+ Goal`**. It gets the ID `Goal-001`.
2. In the objective form choose `Goal-001` and add two:
   - "Go-live for IaaS service", criterion "120 VMs migrated and validated by the client", approver "Lucía Mendaro".
   - "Immutable backup in place", criterion "Test restore passed", approver "Marta Gil".
3. Change the **Completion Status** of the first one to `In Progress` by clicking the cell and pressing **Save**.

**Business outcomes (optional).** In the **Business outcomes** block of the same screen, use the **`+ Outcome`** form: "Cut infrastructure cost", metric "% vs. 2025", target "30", a target date. It gets `OUT-001`; you can edit every cell inline and link it to requirements from the RTM, like an objective.

**Architecture elements.** Sidebar → **Define → Architecture (HLD/LLD)**, **Architecture elements** block:

1. Add "Private cloud cluster" (view `Technology`, HLD "§4.1") and "Backup vault" (view `Data`, HLD "§5.2"). They get `AE-001` and `AE-002`. Names, views, workstreams and HLD/LLD references are editable by clicking them.

**Requirements.** Sidebar → **Define → Requirements & RTM** → expand **Add a requirement or evidence**:

1. `FR`, "Migrate the 120 production VMs without data loss", `Must`, source "SOW §3.1" → **Add requirement** (`REQ-001`).
2. `NFR`, "IaaS service availability", `Must`, category `Availability`, criterion "≥ 99.95 % monthly" (`NFR-001`).
3. `NFR`, "Immutable retention of copies", `Should`, category `Retention`, **no criterion** (`NFR-002`).

Look at the result: all three show up as orphans (`— missing` in red), the **Orphan requirements** KPI is 3, the sidebar badge too, and `NFR-002` shows `Criterion: not measurable yet (RTM-03)`. Go to the dashboard: the **Requirements** control is red, and **Red rules blocking G1** includes RTM-01 for the `Must` requirements.

**Fixing the criterion.** Back in the RTM, click **Edit** on `NFR-002`. The requirement's edit page opens with every field. Write the **Measurable criterion** ("Backups immutable for 30 days; deletion blocked"), optionally the **Target value** and **Achieved value** (they feed the NFR radar on the dashboard), and press **Save**. The RTM-03 alert disappears.

**Linking.** In the RTM, click the **Outcome / Objective** cell of `REQ-001`. A window opens with the objectives and outcomes as checkboxes, grouped, and a search box. Tick `Obj-001` and press **Save** (or `Enter`). Then click its **Architecture Element** cell, search "cluster", tick `AE-001` and **Save**. Repeat for the others:

| Requirement | Link to |
|---|---|
| REQ-001 | Obj-001 and AE-001 |
| NFR-001 | Obj-001 and AE-001 |
| NFR-002 | Obj-002 and AE-002 |

The orphans disappear. The status moves to `Traced (evidence pending)`: evidence is missing, and it will come in the validation phase. Cells that are still empty show `+ link` when you hover or focus them. If you tick something by mistake, open the cell again, untick it and **Save**; **Cancel** or `Esc` close the window without changing anything.

**Revisiting a link in the ADR direction.** The same window exists on an ADR page (**Traceability → Edit links**) to choose which architecture elements the decision affects. You will use it in B7.

**HLD review.** In **Architecture (HLD/LLD)**:

1. **Design reviews** block: type `HLD`, document "HLD Hybrid Cloud", version "0.9" → **Start review**.
2. Open the review. Answer each question by clicking **Answer**. For "Are NFRs measurable…?" answer `Partial` and comment "Retention criterion missing".
3. On that row press **→ ADR**. The app creates a `Proposed` ADR with the review's context and takes you to it. We come back to ADRs in B7.
4. When you finish, mark the review as **Approved** with the button in the header.

Optional: upload a context diagram in **Diagrams by view** (PNG, SVG or draw.io).

### B6. Risks and dependencies

**A well-written risk.** Top bar → **`+ Register` → Risk**:

- Title: "Hardware delivery lead time"
- Cause: "the manufacturer has announced 8-week delays on the storage nodes"
- Event: "the hardware does not arrive before the March migration window"
- Consequence: "delay closing the data centre and pay three more months of rent"

Note the *Risk statement* box: it composes the sentence "Due to…, there is a risk of…, which could lead to…". Set **Probability 3** and **Impact 3** (score 9, red), leave **Owner** and **Response** empty, set a date and a trigger ("No confirmed date 6 weeks before the window") and press **Register**.

You return to the **Risks** tab with the new `RK-001` marked red: **RAID-01** (critical risk with no owner or response), which blocks every gate. That is what a badly registered risk looks like. Fix it in place:

1. Click the **Risk Mitigation Owner** cell and write "Pablo Ortiz".
2. Click the `Response:` line in **Risk Mitigation & Updates** and write "Ask the manufacturer for a written delivery commitment and reserve stock with an alternative distributor". Press **Save**. The RAID-01 alert disappears.
3. Add a dated update with the current status (`Add a dated update` → `Add`).

Also try the **Probability × Impact** matrix: click the 3 × 3 cell to filter.

**Escalating.** Expand **Show cause / event / consequence / trigger / forum** and set **Trigger Met** to `Yes` on RK-001: rule **RAID-04** fires (red, blocks all gates) because the trigger has been met and there is no Escalation. In the risk's row press **Raise escalation to …**: an `ES-` Escalation is created, linked to the risk and due in 7 days, and the alert is resolved. (If the trigger turns out to be a false alarm, set **Trigger Met** back to `No`.)

**An action.** `+ Register` → **Action**: "Confirm the delivery date with the manufacturer", owner, a date next week. It appears in the **Actions** tab.

**An external dependency.** `+ Register` → **Dependency**:

- Dependency: "10 Gb circuit between sites"
- Needed by: "Networking"
- Depends on: "Telecoms operator (fictitious)"
- Type `Delivery`, a date, tick **External** and **On the critical path**, leave **Commitment verified** unticked.

**Dependencies** opens: the arrow is red and rule **DEP-01** is red (it will block G3 and G4). When you have the commitment in writing, set **Commitment verified** = `Yes` and note the evidence. Do it now to leave things clean.

### B7. Decisions (ADR)

Sidebar → **Govern → Decisions (ADR)** → **`+ New ADR`**:

- Decision: "Use a customer-managed HSM for the encryption keys"
- Context: "The data-residency policy requires that keys do not leave the country."
- Escalation level: `Architecture · Design Authority`
- Tick **On the critical path**: the app then forces you to set an **Owner** and a **Decision due date** (otherwise ADR-01 fires).
- Option 1: "Customer on-premises HSM". Option 2: "Cloud provider KMS".

Press **Propose**. On the ADR page:

1. In the **Architecture Decision Record** table complete the **Decision**, the **Rationale** and, very important, **Impacted artifacts** ("HLD §6, RTM, key runbook"). Without impacted artefacts, ADR-03 fires once it is approved.
2. In **Traceability**, press **Edit links** next to *Architecture elements*. In the window, tick `AE-001` (use the search box if the list is long) and **Save**. The requirements shown under *Requirements* are derived from the element, and the ADR now appears in the RTM's **ADR** column for those requirements (as inherited, read-only there).
3. In **Options and trade-offs**, score each option from 1 to 5 on the 7 criteria. The highest total is shown in green. Press **Choose** on the winning option.
4. Do not approve it yet: we will take it to the Design Authority.

### B8. Design Authority

Sidebar → **Govern → Design Authority**.

1. In **Prepare the next session**, keep the proposed date and the ticked ADRs (the HSM one and the one that came out of the HLD review). Press **Create session with selected decisions**. The ADRs move to `Under Review`.
2. In **Sessions**, open the session and press **Start meeting mode**.
3. Start the timer (**Start**) and advance blocks with **Next block** following the 5-10-30-10-5 agenda.
4. In **Quorum**, mark present the Enterprise Architect, the Tech / Domain Leads and the Customer.
5. In **Decisions, one by one**:
   - HSM ADR: Outcome `Approved`, Conditions "Validate HSM latency in the pilot".
   - Review ADR: Outcome `Deferred`, Notes "Client retention criterion missing".
6. In **Actions raised**, add "Send the DA summary to the sponsor", with owner and date.
7. Press **Close session**.

What happened: the HSM ADR is `Approved`, the other `Deferred`, and the RAID has new automatically created actions ("Apply the conditions of ADR-…" and "Prepare ADR-… for re-submission") besides yours. The chip tells you whether there was quorum. Download the minutes with **Minutes · DOCX**.

Open **RAID & Dependencies → Actions** to see the generated actions. (A closed session cannot be deleted: its minutes are the record of what was decided.)

### B9. Baseline and gate G1

1. Go back to **First 10 days** and, in **Result of the first 10 days**, press **Create baseline v1**. It is recorded with date and SHA-256 hash. From now on, changing a requirement or architecture element without linking a Change or an ADR raises the **BASE-01** alert.
2. Open **Gates & Roadmap**. The **G1 · Discover** card is expanded.
   - In **Exit criteria**, change the criterion to `Met` and write the evidence.
   - **Red rules blocking G1** should say "None". If something appears (for example RAID-01 for a risk with no response, or RTM-01), fix it with the link from the dashboard.
3. Press **Pass G1**. The gate becomes `Passed`, the programme moves to **phase 2 · Define target** and G2 becomes `In Progress`.

**Trying a waiver (optional).** If a gate is blocked by something the client knowingly accepts, create an ADR documenting the exception, approve it (`Under Review` → `Approved`), choose it in the gate's **Waiver (approved ADR)** and press **Save**. The blockers are overridden and the gate can be passed.

### B10. Roadmap and phase progress

In **Gates & Roadmap**, **Architecture roadmap** block, create three work packages:

| Work package | Start | End | Milestone | Transition state |
|---|---|---|---|---|
| Private cloud pilot | today | +6 weeks | | TS1 Pilot |
| Migration wave 1 | +6 weeks | +14 weeks | | TS2 Wave 1 |
| Data centre closure | +20 weeks | | ✓ | Target |

They appear in the Gantt chart. Change the pilot's **Status** to `In Progress` with a click.

Pass **G2** (phase 3) and **G3** (phase 4) the same way. On reaching **phase 3**, **OPS-01** appears in red: operations has no owner and is not involved. It does not block G3 or G4, but it will block G5. The guide insists on involving operations early, so fix it now:

- Sidebar → **Deliver & Close → Operational Readiness**. In the **Operations owner** box, click the dash and type "Andrés Peral"; click "Not involved yet" and choose `Yes`.

Involving your operations detractor is, incidentally, the best way to manage them.

### B11. Weekly follow-up (phase 4)

During delivery, your weekly routine in the app is:

1. **Program dashboard**: review the **Weekly health check** and the bell. Fix or plan each finding.
2. **Am I really in charge?**: honestly tick the 8 questions.
3. **RAID**: add dated updates to risks, close what is resolved, review overdue items (RAID-03).
4. **Export → T12 · Weekly Health Check · PPTX** for the status meeting.
5. Prepare the next **Design Authority** with the pending ADRs (ADR-02 warns you if any exceeds the 10-day SLA).

Pass **G4** when you are ready. You enter **phase 5 · Validate / Transition**.

### B12. Validation and Operational Readiness (phase 5)

On entering phase 5, two kinds of blocker appear for G5:

**Evidence (RTM-02).** No requirement can reach acceptance without proof. In **Requirements & RTM → Add a requirement or evidence**, add evidence for each requirement:

- "Wave 1 migration report", `Document`, `Pass`, link to the project folder, requirement `REQ-001`.
- "30-day availability test", `Automated test`, `Pass`, `NFR-001`.
- "Restore from immutable copy", `Demo`, `Pass`, `NFR-002`.

Each requirement becomes `Traced end to end` and the dashboard's **RTM coverage** KPI rises to 100 %. Evidence added for the wrong requirement? Click the **Test / Evidence** cell of the right requirement and tick it there, or untick it from the wrong one.

**Operational readiness (ORR-01).** In **Operational Readiness**, use **`+ Add check`** to add the guide's dimensions: `Monitoring`, `Backup`, `Support`, `Access`, `Capacity`, `Runbooks`, `Skills` (and your own, such as `DR / RTO-RPO`), each with a criterion and an owner. Then, in each row:

- write the **Evidence** ("Runbooks reviewed and signed off by operations on 12/03"),
- change the **Status** to `green`, `amber` or `red`.

Set one to `red` and look at the `G5 blocked` chip and the **G5 is blocked by: ORR-01…** notice. Put it back to `amber` or `green` and the block disappears.

Download **Export → T13 · Operational Readiness Review · XLSX**. When nothing is left in red, pass **G5** in **Gates & Roadmap**.

### B13. As-built, handover and closure (phase 6)

Sidebar → **Deliver & Close → As-built & Handover**.

1. **Baseline vs implemented**: `From` = `Baseline v1`, `To` = `Current (as implemented)`, **Compare**. You will see what has changed since the baseline: new evidence, actions and risks, work packages… It is the reconciliation between what was approved and what was built.
2. **Technical debt, deviations and transfers**: record at least one piece of technical debt, for example `TechDebt` "Manual HSM monitoring", impact "Risk of not detecting failures", cost "5 person-days", receiving owner "Andrés Peral", review date.
3. Review **Open decisions and risks to close or transfer**: close what you can and hand over the rest (as debt or `Transfer`).
4. Tick the **EA Definition of Done** point by point; the counter updates as you go.
5. Download **Export → T14 · As-built & Handover + EA DoD · DOCX**.

### B14. Lessons learned

Sidebar → **Deliver & Close → Lessons Learned**.

1. Record a lesson: "Involve operations from the kick-off", context "The operations head was a detractor", decision "Name them ORR owner in phase 3", outcome "ORR green first time", recommendation "Assign the operations owner before G2".
2. In **From other programs**, search "backup" and press **Reuse** on a lesson from another programme to bring it into this one as a starting point.

### B15. Tidying up, templates and backup

1. **Deleting things you created by mistake.** Rows that can be deleted show a trash icon. Click it on a test record (for example a duplicate risk): a confirmation page lists what depends on it, which relations will be removed and what is deleted with it; if something blocks the deletion it tells you what to resolve first. Confirm with **Delete**, or **Cancel**. Every deletion goes into the audit log.
2. Sidebar → **Enablement → Template Library**. Filter by `Format: XLSX` or by phase and try **Generate with program data** on several templates. Note the file name: `{Programme}_{Artefact}_v{version}_{YYYYMMDD}`. If the client needs them in Spanish, use the **ES** link in each screen's Export menu, or change the programme's export language in Administration.
3. Sidebar → **Administration** → the `HCM-NDC` row → **Export**. Save the JSON: it is the complete copy of the programme, which you can import into another installation with **Import a program**. For a copy of all the data, use `scripts/backup.sh` (see [INSTALL.md](INSTALL.md)).
4. Pass **G6** when the client accepts the closure.

Congratulations! You have gone through the full lifecycle of an engagement.

---

## Summary: which screen to use when

| Moment of the engagement | Screens |
|---|---|
| Start-up (days 1-10) | First 10 days · Stakeholders, RACI & Comms · Architecture Brief |
| Discover / Define | Goals & SDM · Requirements & RTM · Architecture (HLD/LLD) · RAID |
| Deciding | Decisions (ADR) · Design Authority |
| Weekly control | Program dashboard · RAID & Dependencies · alert bell |
| Changing phase | Gates & Roadmap |
| Validating and transferring | Requirements & RTM (evidence) · Operational Readiness |
| Closing | As-built & Handover · Lessons Learned · Template Library |

## If something does not go as expected

- **A gate will not let you pass**: read the red message or the **Red rules blocking G*n*** list; each rule links to the item to correct. The [rules table in the manual](MANUAL.md#11-governance-rules-catalogue) says how to fix each one.
- **I cannot find an item**: use the top-bar search (**Ctrl K**) with its ID (`RK-001`, `ADR-002`…).
- **A RAID-04 alert will not go away**: press **Raise escalation** on the risk's row, set **Trigger Met** back to `No`, or close the risk. See the [manual](MANUAL.md#74-raid--dependencies).
- **I cannot delete a record**: the confirmation page tells you which records depend on it; resolve those first. See [Deleting records and removing links](MANUAL.md#deleting-records-and-removing-links).
- **I messed up a demo**: `make seed`.
