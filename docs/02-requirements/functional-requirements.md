# Functional Requirements

> Source: TechXplo – Claims Management Portal BRD v0.7 (17 Sep 2026), Section 7 ("Functional Requirements"),
> cross-referenced with Section 4 ("System Modules"). All 33 requirements were confirmed with the Client
> during the requirements sessions (see Appendix B/E–G/I change logs in the source BRD).
>
> **ID convention note**: This project's requirement docs are seeded directly from the BRD, which numbers
> requirements FR-01–FR-33 (not the 3-digit `FR-XXX` example previously in this file). To preserve direct,
> unambiguous traceability back to the 114-page source BRD that the Client will keep referencing, this doc
> keeps the BRD's native IDs rather than renumbering to FR-001 etc. Any **new** requirement raised beyond
> the BRD should continue the sequence from **FR-34** onward, following the field structure below and the
> template in [`templates/functional-requirement-template.md`](templates/functional-requirement-template.md).
>
> Acceptance criteria below are intentionally short pointers to the corresponding Use Case and Test Case —
> full step-by-step scenarios live in [`use-cases.md`](use-cases.md) and [`../07-testing/test-cases.md`](../07-testing/test-cases.md)
> respectively, per this repo's convention of linking rather than duplicating content.

---

### FR-01: Online Claim Registration

| Field | Value |
|---|---|
| **ID** | FR-01 |
| **Status** | Approved (client-confirmed, BRD v0.7) |
| **Priority** | High |
| **Owner** | Aris Brokers (Claims Manager) |
| **Related user role(s)** | Client, Broker/User, Administrator |
| **Related requirement(s)** | FR-02, FR-03, FR-21, FR-33 |

**Description**

The portal shall allow an authenticated Client user, or a Broker acting on the Client's behalf, to register
a new claim by capturing the policy number, the date of loss, the location of loss, a description of the
incident, and the claim type, with photographs and any incident or police reports attached at the point of
lodgement. Claims are received today by email, telephone, and WhatsApp, so Broker-initiated registration is
required.

**Rationale**

Digitises the current manual, email/phone/WhatsApp-based claim intake described in Section 6 (Current
State).

**Acceptance criteria**

- [ ] See [`use-cases.md#uc-01--submit-new-claim`](use-cases.md#uc-01--submit-new-claim) (Client-initiated) and [`use-cases.md#uc-15--brokeradministrator-lodges-claim-on-behalf-of-client`](use-cases.md#uc-15--brokeradministrator-lodges-claim-on-behalf-of-client) (Broker-assisted).
- [ ] Verified by [`../07-testing/test-cases.md#tc-01`](../07-testing/test-cases.md#tc-01) and [`../07-testing/test-cases.md#tc-15`](../07-testing/test-cases.md#tc-15).

**Out of scope**

Assessor appointment, insurer notification mechanics (remains offline via email in Phase 1).

---

### FR-02: Automated In-House Claim Reference Number

| Field | Value |
|---|---|
| **ID** | FR-02 |
| **Status** | Approved |
| **Priority** | High |
| **Owner** | Aris Brokers |
| **Related user role(s)** | Client, Broker/User |
| **Related requirement(s)** | FR-01 |

**Description**

Upon submission, the system shall automatically generate a unique in-house claim reference number
(distinct from the insurer's claim number) and present it to the Client for future correspondence.

**Rationale** — Confirmed by the Client, session 02.

**Acceptance criteria**

- [ ] See [`use-cases.md#uc-01--submit-new-claim`](use-cases.md#uc-01--submit-new-claim); verified by TC-01.
- [ ] The in-house number cannot be edited by the Client.

**Out of scope** — Insurer claim number allocation (see FR-06).

---

### FR-03: Required-Document Checklist

| Field | Value |
|---|---|
| **ID** | FR-03 |
| **Status** | Approved |
| **Priority** | High |
| **Owner** | Aris Brokers |
| **Related user role(s)** | Client, Broker/User |
| **Related requirement(s)** | FR-01, FR-04, FR-21 |

**Description**

Based on the claim type and product selected, the system shall present a checklist of the documents
typically required to substantiate the claim, indicating which items are outstanding or received. The
checklist is **advisory**: documents shall not be mandatory and an incomplete checklist shall not prevent a
claim from being registered or from progressing.

**Rationale** — Reflects the per-product document lists in
[`product-document-requirements.md`](product-document-requirements.md) and the generic stage-based mapping
in [`data-requirements.md#128-document-requirements--responsibility-matrix`](data-requirements.md#128-document-requirements--responsibility-matrix).

**Acceptance criteria**

- [ ] See UC-01, UC-02; verified by TC-01, TC-02.
- [ ] A claim may be submitted/progressed with outstanding checklist items.

**Out of scope** — Blocking validation of documents (explicitly excluded — checklist is advisory only).

---

### FR-04: Document Management (Upload / Store / Retrieve)

| Field | Value |
|---|---|
| **ID** | FR-04 |
| **Status** | Approved |
| **Priority** | High |
| **Owner** | Aris Brokers |
| **Related user role(s)** | Client, Broker/User |
| **Related requirement(s)** | FR-03 |

**Description**

The portal shall allow Clients and Brokers to upload, store, retrieve and download claim-related documents
against the relevant claim record. Every uploaded document must be labelled by the uploader so that it can
be identified without being opened.

**Rationale** — Centralises documents currently scattered across email and the Lucid/OneDrive-style
repository (Objective 5, Section 2).

**Acceptance criteria**

- [ ] See UC-02; verified by TC-02.

**Out of scope** — Document storage architecture/volume handling (a design-phase decision — see
[Architectural Considerations](../03-architecture/README.md); functional requirement here is upload/store/
retrieve behaviour only).

---

### FR-05: Claims Tracking & Status Management

| Field | Value |
|---|---|
| **ID** | FR-05 |
| **Status** | Approved |
| **Priority** | High |
| **Owner** | Aris Brokers |
| **Related user role(s)** | Client, Broker/User |
| **Related requirement(s)** | FR-06 |

**Description**

The system shall maintain a defined set of claim status stages (see
[`process-flow.md#claim-status-stages-client-facing`](process-flow.md#claim-status-stages-client-facing))
and update the Client-facing dashboard as the claim progresses. The stage set includes Disputed, Awaiting
Excess Invoice and Proof of Payment, and Not Taken Up (NTU).

**Rationale** — Directly addresses Objective 2 (real-time client visibility).

**Acceptance criteria**

- [ ] See UC-03, UC-04; verified by TC-03, TC-04.
- [ ] Closed claims are view-only to Client users.

**Out of scope** — Exact client-facing wording of each stage remains an open item (see
[`open-questions.md`](open-questions.md)).

---

### FR-06: Insurer Claim Number Linking

| Field | Value |
|---|---|
| **ID** | FR-06 |
| **Status** | Approved |
| **Priority** | Medium |
| **Owner** | Aris Brokers |
| **Related user role(s)** | Broker/User |
| **Related requirement(s)** | FR-05 |

**Description**

The system shall allow an authorised Broker user to capture the Insurer-allocated claim number and link it
to the corresponding in-house claim record, and shall communicate that Insurer claim number to the Client.

**Acceptance criteria**

- [ ] See UC-04; verified by TC-04. Status cannot progress to "Submitted to Insurer" without this field.

---

### FR-07: Automated Reminders & Notifications

| Field | Value |
|---|---|
| **ID** | FR-07 |
| **Status** | Approved |
| **Priority** | Medium |
| **Owner** | Aris Brokers |
| **Related user role(s)** | Client, Broker/User (recipients); Administrator (configures) |
| **Related requirement(s)** | FR-20, FR-33; NFR-11 |

**Description**

The system shall automatically generate reminders and notifications to the responsible party **by outbound
email only**. In-app and SMS notification are explicitly out of scope — the system sends outbound email and
does not maintain an in-portal notification channel. Reminders are required where a claim has outstanding
documents or information, or has had no status update for a configurable period, and at the following
events: acknowledgement of documents received; request for outstanding information; allocation of the
Insurer claim number; appointment of an assessor; the Insurer's decision; issue of the excess invoice; and
receipt of proof of payment.

**Rationale** — Module 6 (Notifications & Reminders) is a background service, not a user-facing screen; see
[`process-flow.md`](process-flow.md#architecture-note-notifications--reminders-module-6).

**Acceptance criteria**

- [ ] See UC-13; verified by TC-13.
- [ ] Reminder frequency/threshold is Administrator-configurable (see [`data-requirements.md#1211-company--report-settings`](data-requirements.md#1211-company--report-settings)).

**Out of scope** — In-app or SMS channels; two-way email sync (open item).

---

### FR-08: Client-Broker Communication Log

| Field | Value |
|---|---|
| **ID** | FR-08 |
| **Status** | Approved |
| **Priority** | Medium |
| **Owner** | Aris Brokers |
| **Related user role(s)** | Client, Broker/User |
| **Related requirement(s)** | FR-20, FR-27 |

**Description**

The portal shall provide a claim-specific communication/notes feature enabling the Client and assigned
Broker to exchange messages, with all entries timestamped and retained against the claim record.

**Acceptance criteria**

- [ ] See UC-12; verified by TC-12.

**Out of scope** — This is a conversational thread, distinct from the reportable Broker/Client Comment
fields in FR-27.

---

### FR-09: Client & Policy Management

| Field | Value |
|---|---|
| **ID** | FR-09 |
| **Status** | Approved |
| **Priority** | High |
| **Owner** | Aris Brokers |
| **Related user role(s)** | Administrator, Broker/User |
| **Related requirement(s)** | FR-10 |

**Description**

The system shall maintain a record per Client (SOE) including policy number(s), policy period, insurer(s),
class of insurance, section(s), sums insured/limit of indemnity, excess(es) and applicable terms/conditions,
and allow policy schedule documents to be uploaded for reference and selected at claim submission.

**Acceptance criteria**

- [ ] See UC-09; verified by TC-09.

---

### FR-10: Broker-Client Allocation

| Field | Value |
|---|---|
| **ID** | FR-10 |
| **Status** | Approved |
| **Priority** | High |
| **Owner** | Aris Brokers |
| **Related user role(s)** | Administrator, Manager, Broker/User |
| **Related requirement(s)** | FR-09 |

**Description**

The system shall allow each Client (SOE) to be assigned to a specific Broker/claims handler, so that Broker
users can only view and manage their own assigned clients. The Administrator and Manager can view all
claims across all brokers, which is the mechanism by which a claim is picked up when its assigned Broker is
unavailable.

**Acceptance criteria**

- [ ] See UC-09; verified by TC-09/TC-10.

---

### FR-11: Assessor Recording

| Field | Value |
|---|---|
| **ID** | FR-11 |
| **Status** | Approved |
| **Priority** | Low |
| **Owner** | Aris Brokers |
| **Related user role(s)** | Broker/User |
| **Related requirement(s)** | — |

**Description**

The system shall allow the Broker to capture the Insurer-appointed Assessor's details against the relevant
claim via three simple capture fields: Assessor Name/Company, Assessor Contact Details, and Date Appointed.
This is for accountability and traceability only — assessor appointment itself remains the Insurer's
function, outside the system. No assessor workflow, scheduling, or assessor-facing access is included in
Phase 1.

**Acceptance criteria**

- [ ] See UC-04, UC-06; verified by TC-04.

---

### FR-12: Settlement & Agreement of Loss (AOL) Workflow

| Field | Value |
|---|---|
| **ID** | FR-12 |
| **Status** | Approved |
| **Priority** | High |
| **Owner** | Aris Brokers |
| **Related user role(s)** | Broker/User, Client |
| **Related requirement(s)** | FR-25 |

**Description**

The system shall support recording the Insurer's decision as **Settled** (cash, repair or replacement),
**Repudiated** (the claim is declined by the Insurer, with a mandatory Repudiation Reason captured), or
**Within Excess** (the loss falls within or below the policy excess, so the Insurer is not liable and the
Client bears the cost), and shall notify the Client of the outcome.

- Where settled in cash: the Broker uploads an Agreement of Loss for the Client to download, the Client
  uploads the signed Agreement of Loss, and the Broker records the Insurer's proof of payment and shares it
  with the Client; no invoicing arises on a cash settlement.
- Where settled as a repair or replacement: the Broker issues the excess invoice to the Client, the Client
  uploads proof of payment of the excess, and the Broker records that the proof of payment has been
  forwarded to the service provider. The system notifies the Client only and does not correspond with the
  service provider.

**Acceptance criteria**

- [ ] See UC-05, UC-06, UC-07; verified by TC-05, TC-06, TC-07.
- [ ] A "Repudiated" outcome cannot be saved without a Repudiation Reason.

---

### FR-13: Claim Closure & Archival

| Field | Value |
|---|---|
| **ID** | FR-13 |
| **Status** | Approved |
| **Priority** | Medium |
| **Owner** | Aris Brokers |
| **Related user role(s)** | Broker/User, Administrator |
| **Related requirement(s)** | FR-12 |

**Description**

The system shall allow an authorised Broker/Administrator to close a claim once its outcome is fully
resolved. A closed claim shall remain open to notes and comments so that post-closure events can be
recorded — for example that the claim is under subrogation where the Insurer is recovering the loss from a
third party, or that the Client is due a refund of their excess. Closed claims are otherwise view-only to
Client users, remain editable only by an Administrator, and are retained for a minimum of five years per
FSCA requirements.

**Acceptance criteria**

- [ ] See UC-07; verified by TC-07.
- [ ] A Settled claim cannot close without Proof of Payment on file; a Repudiated claim cannot close
      without a Repudiation Reason on file.

---

### FR-14: Reporting — Claims History Report

| Field | Value |
|---|---|
| **ID** | FR-14 |
| **Status** | Approved |
| **Priority** | High |
| **Owner** | Aris Brokers |
| **Related user role(s)** | Client, Broker/User, Manager |
| **Related requirement(s)** | FR-22, FR-28, FR-29 |

**Description**

The system shall generate a downloadable Claims History Report (claims register) per client as a single
workbook of two tabs, in the format supplied by the Client (Claim Register Template): an Index & Key tab and
a Summary tab (the all-claims register). Every cell of that template must be populated from data held in the
system — see [`data-requirements.md#appendix-d-style-report-field-provenance`](data-requirements.md) for
field-by-field provenance and the BRD's Appendix H for cell-level layout. The report covers motor and
non-motor claims together.

**Acceptance criteria**

- [ ] See UC-08; verified by TC-08.

---

### FR-15: Reporting — Performance Report

| Field | Value |
|---|---|
| **ID** | FR-15 |
| **Status** | Approved |
| **Priority** | High |
| **Owner** | Aris Brokers |
| **Related user role(s)** | Client, Broker/User, Manager |
| **Related requirement(s)** | FR-16, FR-22, FR-28, FR-29 |

**Description**

The system shall generate a downloadable Performance Report per client as a single workbook of four tabs,
in the format supplied by the Client (Performance Reporting Template): a Claim-Loss Ratio tab, a Summary
Report tab, a Non-Motor Claims History tab and a Motor Claims History tab, in that order. The Matters
Arising tab is a meeting action register rather than claims data and is excluded from the system; it
remains maintained by the Broker outside the portal.

**Acceptance criteria**

- [ ] See UC-08; verified by TC-08.

---

### FR-16: Reporting — Claim-Loss Ratio Tab

| Field | Value |
|---|---|
| **ID** | FR-16 |
| **Status** | Approved |
| **Priority** | Medium |
| **Owner** | Aris Brokers |
| **Related user role(s)** | Client, Broker/User, Manager |
| **Related requirement(s)** | FR-15, FR-24 |

**Description**

The Claim-Loss Ratio tab of the Performance Report shall show, per underwriting year, the number of claims,
the total claim value, the risk premium paid, the payment frequency and the resulting portfolio loss ratio,
calculated as total claim value divided by risk premium paid. Risk premium is captured per policy per
underwriting year (FR-24). On a consolidated report the premium figures of every policy in scope are summed
to give a portfolio-level ratio; on a single-policy report only that policy's premium is used. The loss
ratio cannot be produced for an underwriting year in which no premium has been captured, and shall be shown
as unavailable rather than zero.

**Acceptance criteria**

- [ ] See UC-08; verified by TC-08.

---

### FR-17: User Role & Access Management

| Field | Value |
|---|---|
| **ID** | FR-17 |
| **Status** | Approved |
| **Priority** | High |
| **Owner** | Aris Brokers |
| **Related user role(s)** | Administrator |
| **Related requirement(s)** | NFR-01, NFR-02 |

**Description**

The system shall support creation, modification and deactivation of user accounts and role-based permission
assignment (Administrator, Manager, Broker/User, Client-Primary, Client-Secondary), enforcing
least-privilege access and MFA at login.

**Acceptance criteria**

- [ ] See UC-10; verified by TC-10. Full role/permission matrix: [`user-roles.md`](user-roles.md).

---

### FR-18: Audit Trail

| Field | Value |
|---|---|
| **ID** | FR-18 |
| **Status** | Approved |
| **Priority** | High |
| **Owner** | Aris Brokers |
| **Related user role(s)** | Administrator, Manager |
| **Related requirement(s)** | NFR-08, NFR-09 |

**Description**

The system shall automatically log all material actions against a claim or client/policy record (creation,
edits, status changes, document uploads, communications), capturing who performed the action, what changed,
and when, viewable to authorised roles and filterable by the responsible Broker.

**Acceptance criteria**

- [ ] See UC-14; verified by TC-14. Audit entries are immutable and retained for a minimum of 5 years.

---

### FR-19: Late-Reporting Validation (30-Day Rule)

| Field | Value |
|---|---|
| **ID** | FR-19 |
| **Status** | Approved |
| **Priority** | Medium |
| **Owner** | Aris Brokers |
| **Related user role(s)** | Client, Broker/User |
| **Related requirement(s)** | — |

**Description**

The system shall compare the date of loss to the date of claim registration and flag the claim as Late
Reported where the gap exceeds 30 days. Where a claim is flagged, the Broker shall be able to capture a
written motivation for the delay against the claim. The claim is not blocked from submission.

**Acceptance criteria**

- [ ] See UC-11; verified by TC-11. Flagging is advisory, not blocking.

---

### FR-20: Outbound Email Integration (Outlook)

| Field | Value |
|---|---|
| **ID** | FR-20 |
| **Status** | Approved |
| **Priority** | Medium |
| **Owner** | Aris Brokers |
| **Related user role(s)** | Broker/User |
| **Related requirement(s)** | FR-07, FR-08 |

**Description**

The system shall integrate with the Broker's Outlook mailbox so that communications sent from the portal to
a Client are delivered to and visible within the Client's normal email inbox. Two-way sync (inbound replies
appearing automatically in the portal) is an open item.

**Acceptance criteria**

- [ ] See UC-12; verified by TC-12.

---

### FR-21: Asset Register

| Field | Value |
|---|---|
| **ID** | FR-21 |
| **Status** | Approved |
| **Priority** | Medium |
| **Owner** | Aris Brokers |
| **Related user role(s)** | Administrator, Broker/User |
| **Related requirement(s)** | FR-01, FR-09 |

**Description**

The system shall maintain an asset register per policy so that the specific asset(s) being claimed against
can be selected at the point of claim submission. A single claim may cover more than one asset on the same
policy — three vehicles damaged in one incident is one claim. An item that is not on the asset register is
not covered, and the system shall make that position clear at lodgement.

**Acceptance criteria**

- [ ] See UC-01, UC-09; verified by TC-01, TC-09.

---

### FR-22: Report Generation Engine

| Field | Value |
|---|---|
| **ID** | FR-22 |
| **Status** | Approved |
| **Priority** | High |
| **Owner** | Aris Brokers |
| **Related user role(s)** | Client, Broker/User, Manager |
| **Related requirement(s)** | FR-14, FR-15 |

**Description**

The system shall generate the reports defined in FR-14 and FR-15 as two separate multi-tab spreadsheet
workbooks — one Claims History Report workbook and one Performance Report workbook — each reproducing the
tab names, tab order, merged header structure, column order, column widths, totals rows, status
colour-coding and key blocks of the template supplied by the Client, as specified in the BRD's Appendix H.
Values are written as stored or calculated data, not as spreadsheet formulas, so that each generated
workbook is self-contained. At generation the user shall select (a) the reporting period — defaulting to
the current underwriting year, with any arbitrary start and end date permitted — and (b) the report scope,
being either a consolidated report covering all of the client's policies or a report for one named policy.

**Acceptance criteria**

- [ ] See UC-08; verified by TC-08.

---

### FR-23: Underwriting Year Derivation

| Field | Value |
|---|---|
| **ID** | FR-23 |
| **Status** | Approved |
| **Priority** | High |
| **Owner** | Aris Brokers |
| **Related user role(s)** | System (calculated) |
| **Related requirement(s)** | FR-16 |

**Description**

The system shall derive an Underwriting Year for every claim by comparing the date of loss to the policy
inception date, the year rolling annually on the anniversary of inception rather than on the calendar year.
The underwriting year is used to group claims in the Claim-Loss Ratio tab and appears as "Insured Year" on
the Claims History Report.

**Acceptance criteria**

- [ ] See UC-08; verified by TC-08.

---

### FR-24: Risk Premium & Payment Frequency Capture

| Field | Value |
|---|---|
| **ID** | FR-24 |
| **Status** | Approved |
| **Priority** | High |
| **Owner** | Aris Brokers |
| **Related user role(s)** | Broker/User |
| **Related requirement(s)** | FR-16 |

**Description**

The system shall allow an authorised Broker user to capture, per policy and per underwriting year, the risk
premium paid and the premium payment frequency. These values exist nowhere in the current process record
and are required for the loss ratio calculation in FR-16.

**Acceptance criteria**

- [ ] See UC-09; verified by TC-09.

---

### FR-25: Claim Financial Values

| Field | Value |
|---|---|
| **ID** | FR-25 |
| **Status** | Approved |
| **Priority** | High |
| **Owner** | Aris Brokers |
| **Related user role(s)** | Broker/User |
| **Related requirement(s)** | FR-12 |

**Description**

The system shall hold, per claim, the gross claim amount, the excess applied, the VAT amount and the net
claim amount. Gross claim and VAT are captured by the Broker — VAT is entered manually from the insurer or
repairer invoice and is not calculated by the system. Excess defaults from the policy section and may be
overridden by the Broker. Net claim is calculated by the system as gross claim less excess plus VAT.

**Acceptance criteria**

- [ ] See UC-05, UC-08; verified by TC-05, TC-08.

---

### FR-26: Motor Claim Detail Capture

| Field | Value |
|---|---|
| **ID** | FR-26 |
| **Status** | Approved |
| **Priority** | High |
| **Owner** | Aris Brokers |
| **Related user role(s)** | Client, Broker/User |
| **Related requirement(s)** | — |

**Description**

The system shall capture, against a motor claim, the name of the person involved in the incident, the
police case number where one exists, and a free-text description of the loss. The person involved may be
the policyholder, an employee of the insured, or another party entirely, and is therefore captured as free
text rather than as a link to a system user or asset record; it is not applicable on every claim. The
vehicle identification number and licence plate are held on the asset register and pulled onto the claim,
not re-entered.

**Acceptance criteria**

- [ ] See UC-01, UC-09; verified by TC-01, TC-09.

---

### FR-27: Separate Broker and Client Comment Streams

| Field | Value |
|---|---|
| **ID** | FR-27 |
| **Status** | Approved |
| **Priority** | Medium |
| **Owner** | Aris Brokers |
| **Related user role(s)** | Client, Broker/User |
| **Related requirement(s)** | FR-08 |

**Description**

The system shall maintain two distinct comment fields against each claim — a Broker comment and a Client
comment — each independently editable by its own party and each reproduced as its own column in the
generated reports. The Client comment is captured by the Client through a "leave a comment" action at the
foot of the claim, and remains available after the claim has been closed (FR-13). This is separate from the
claim communication log in FR-08, which is a conversational thread rather than a reportable field.

**Acceptance criteria**

- [ ] See UC-08, UC-12; verified by TC-08, TC-12.

---

### FR-28: Generated Report Persistence

| Field | Value |
|---|---|
| **ID** | FR-28 |
| **Status** | Approved |
| **Priority** | High |
| **Owner** | Aris Brokers |
| **Related user role(s)** | System |
| **Related requirement(s)** | FR-14, FR-15 |

**Description**

Every report generated under FR-14 or FR-15 shall be stored against the client record with its generation
date, the user who generated it, and the reporting period covered, and shall remain retrievable and
unchanged thereafter. A report reproduces the data as it stood when generated; regenerating for the same
period produces a new stored instance rather than replacing the previous one.

**Acceptance criteria**

- [ ] See UC-08; verified by TC-08.

---

### FR-29: Report Header, Branding and Legend

| Field | Value |
|---|---|
| **ID** | FR-29 |
| **Status** | Approved |
| **Priority** | Medium |
| **Owner** | Aris Brokers |
| **Related user role(s)** | System |
| **Related requirement(s)** | FR-31 |

**Description**

The system shall populate the header block of each generated report from stored records — insured name,
product, policy term, inception date and policy number(s) from the client and policy records, and the
broker's registered FSP licence number and logo from the company settings area (FR-31) — together with the
reporting period covered, the report date, the disclaimer text and the status colour legend. Every report is
addressed to the client's designated Primary contact. On a consolidated report covering several policies the
policy number shall render as "Various"; on a single-policy report it shall render that policy's number.

**Acceptance criteria**

- [ ] See UC-08; verified by TC-08.

---

### FR-30: Progress Classification

| Field | Value |
|---|---|
| **ID** | FR-30 |
| **Status** | Approved |
| **Priority** | Medium |
| **Owner** | Aris Brokers |
| **Related user role(s)** | System (calculated) |
| **Related requirement(s)** | — |

**Description**

The system shall derive a Progress value for every claim of either Outstanding (the claim is not yet
approved) or Resolved (the claim is resolved and closed), calculated from the claim status rather than
captured separately. The Summary Report tab presents these counts split by class of insurance, giving
Outstanding, Resolved and Total for non-motor claims and the same three for motor claims.

**Acceptance criteria**

- [ ] See UC-08; verified by TC-08.

---

### FR-31: Company & Report Settings

| Field | Value |
|---|---|
| **ID** | FR-31 |
| **Status** | Approved |
| **Priority** | High |
| **Owner** | Aris Brokers (Administrator) |
| **Related user role(s)** | Administrator |
| **Related requirement(s)** | FR-29 |

**Description**

The system shall provide an Administrator-maintained settings area holding the broker organisation's own
details and the standard report content, so that these are captured once and pulled into every generated
report rather than hard-coded: registered company name, trading name, FSP licence number, company
registration number, logo, contact details, the disclaimer text for each report type, the VAT rate label
used as a report column heading, the default reminder interval, and the mapping of claim status to report
status label and colour. Only the Administrator may view or change these settings. Full field list:
[`data-requirements.md#1211-company--report-settings`](data-requirements.md#1211-company--report-settings).

**Acceptance criteria**

- [ ] See UC-08, UC-10; verified by TC-08, TC-10.

---

### FR-32: Report Layout Fidelity

| Field | Value |
|---|---|
| **ID** | FR-32 |
| **Status** | Approved |
| **Priority** | High |
| **Owner** | Aris Brokers |
| **Related user role(s)** | System |
| **Related requirement(s)** | FR-22 |

**Description**

Each generated workbook shall reproduce the layout of the Client-supplied template exactly as set out in the
BRD's Appendix H: tab names and order, the two-row merged column headers, the cell addresses of every header
block value, the first data row, the position of totals rows and colour-key blocks, and the column widths. A
generated workbook opened alongside the Client's own template must be indistinguishable in structure,
differing only in the claim data it contains.

**Acceptance criteria**

- [ ] See UC-08; verified by TC-08.

---

### FR-33: Broker/Administrator-Assisted Claim Lodgement

| Field | Value |
|---|---|
| **ID** | FR-33 |
| **Status** | Approved |
| **Priority** | High |
| **Owner** | Aris Brokers |
| **Related user role(s)** | Broker/User, Administrator |
| **Related requirement(s)** | FR-01, FR-07, FR-20 |

**Description**

Where a Broker-User or Administrator lodges a new claim on behalf of a Client (per FR-01), the system shall:
capture the identity and role of the lodging user, distinct from the Client on whose policy the claim is
registered; record the lodgement channel ("Client Self-Service" or "Broker-Assisted"); and automatically
notify the Client-Primary contact by outbound email (FR-20, FR-07) confirming that a claim has been lodged
on their behalf, with a summary of the claim details and a link to view it. No Client confirmation step is
required before the claim proceeds. This applies across all 29 confirmed products.

**Acceptance criteria**

- [ ] See UC-15; verified by TC-15.

---

*Priority definitions (as confirmed by the Client): High = required for a viable Phase 1 launch; Medium =
important but could follow shortly after go-live if needed; Low = beneficial, can be deferred without
materially affecting the core workflow.*
