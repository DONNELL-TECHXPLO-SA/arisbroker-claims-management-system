# Use Cases

> Source: TechXplo – Claims Management Portal BRD v0.7 (17 Sep 2026), Section 8. Each use case realises one
> or more [Functional Requirements](functional-requirements.md) and is verified by the corresponding
> [Test Case](../07-testing/test-cases.md). See [`traceability-matrix.md`](traceability-matrix.md) for the
> consolidated cross-reference.
>
> **ID convention note**: kept as BRD-native UC-01–UC-15 (no `UC-XXX` template exists yet in this project's
> `templates/` folder). These sit alongside, and can be read as a richer complement to, the Gherkin-style
> `US-XXX` format in [`templates/user-story-template.md`](templates/user-story-template.md) for any future
> informal user stories raised outside the BRD.

---

## UC-01 — Submit New Claim

| Field | Value |
|---|---|
| **Linked FRs** | FR-01, FR-02, FR-03, FR-21 |
| **Primary actor** | Client (Insured – SOE user) |
| **Secondary actor(s)** | System (Claims Portal); Broker (notified) |
| **Pre-conditions** | Client is registered and authenticated (MFA). At least one active policy (and, where applicable, at least one registered asset under that policy) is loaded against the Client's profile. |
| **Trigger** | Client experiences a loss/incident and elects to lodge a claim via the portal. |

**Business rules**

1. 30-day reporting rule flag applies (see FR-19 / UC-11).
2. A claim cannot be submitted without selecting a policy and section; where the product requires it, the
   specific asset (e.g. vehicle, building) being claimed against must also be selected.
3. The in-house claim number is system-generated and cannot be edited by the Client.

**Success end condition** — Claim record is created with a unique in-house claim number, status =
"Submitted", document checklist generated, and the assigned Broker notified.

**Failed end condition** — Claim is not saved due to incomplete mandatory fields, session timeout, or
system error; Client is prompted to correct and resubmit.

**Steps** — Client logs in (MFA) → selects "New Claim" → selects policy/section and, where applicable, the
specific asset → enters loss details (date of loss, location, narrative, claim type) → system validates
mandatory fields and checks date of loss against the 30-day rule → system generates a unique in-house claim
reference number → system generates the required-document checklist based on claim type/product → system
sets claim status to "Submitted" and notifies the assigned Broker → Client receives on-screen confirmation
and the claim reference number.

---

## UC-02 — Upload Supporting Documents

| Field | Value |
|---|---|
| **Linked FRs** | FR-03, FR-04 |
| **Primary actor** | Client |
| **Secondary actor(s)** | Broker; System |
| **Pre-conditions** | Claim already registered (UC-01). A document checklist exists against the claim. |
| **Trigger** | Client has a required document ready to submit, or the Broker requests an additional/ad-hoc document. |

**Business rules**

1. Only configured file types/sizes are accepted.
2. Each uploaded document is timestamped and linked to the claim and the relevant checklist item.
3. Ad-hoc/non-standard document requests (e.g. tracker report) may be added by the Broker as a custom
   checklist item.

**Success end condition** — Document is stored against the claim, the checklist item is marked "Received",
and an audit trail entry is created.

**Failed end condition** — Upload fails (unsupported format, size limit, connectivity) and the Client is
notified to retry.

**Steps** — Client/Broker opens the relevant claim → selects the outstanding checklist item (or "Add
document") → selects the file(s) and uploads → system validates file type/size (and virus-scans, if
configured) → system stores the document, links it to the claim and checklist item, and marks the item
"Received" → system logs the action in the audit trail. The checklist is advisory: claim status is updated
by the Broker and is not blocked by outstanding checklist items (FR-03).

---

## UC-03 — Track Claim Status

| Field | Value |
|---|---|
| **Linked FRs** | FR-05 |
| **Primary actor** | Client |
| **Secondary actor(s)** | System |
| **Pre-conditions** | Client is authenticated. At least one claim exists on the Client's profile. |
| **Trigger** | Client logs in and navigates to "My Claims". |

**Business rules**

1. Client may only view claims linked to their own organisation.
2. Closed claims are view-only.
3. Status displayed must reflect the latest Broker update.

**Success end condition** — Client views the current status, stage history, and any outstanding items for
the selected claim.

**Failed end condition** — No claims found, or system unavailable — an appropriate message is displayed.

**Steps** — Client logs in (MFA) → navigates to "My Claims" → system displays the list of claims with
current status per claim → Client selects a specific claim to view the full stage history/timeline and
outstanding items → Client may add a note/query against the claim, or view/download documents.

---

## UC-04 — Process & Forward Claim to Insurer

| Field | Value |
|---|---|
| **Linked FRs** | FR-05, FR-06, FR-11 |
| **Primary actor** | Broker |
| **Secondary actor(s)** | Insurer (offline, via email); Assessor; System |
| **Pre-conditions** | Claim submitted by Client (UC-01) with required documents uploaded (UC-02). |
| **Trigger** | Broker reviews their claims queue or is notified of a new/updated claim. |

**Business rules**

1. Only the assigned Broker (or Manager/Administrator) may action the claim.
2. The Insurer claim number is mandatory before status can progress to "Submitted to Insurer".
3. Assessor detail is captured via three simple fields only (Name/Company, Contact Details, Date
   Appointed) — optional but recommended for accountability. No assessor appointment, scheduling, or
   assessor-facing functionality is included in Phase 1.

**Success end condition** — Claim record is updated with the Insurer claim number and (once known) the
three assessor detail fields; status progresses; Client is notified.

**Failed end condition** — Key documents are still outstanding; the Broker flags the claim "Documents
Outstanding" and requests them, but the claim is not blocked from progressing (FR-03).

**Steps** — Broker opens the claim from their queue → reviews uploaded documents against the checklist →
Broker sends the claim and documents to the Insurer via email (Phase 1, outside the system) → Broker
receives the Insurer's claim number and (later) assessor appointment details via email → Broker captures the
Insurer claim number and, once known, the three assessor fields on the portal → system updates claim status
to "Submitted to Insurer" / "Under Assessment" and notifies the Client.

---

## UC-05 — Record Insurer Decision

| Field | Value |
|---|---|
| **Linked FRs** | FR-12 |
| **Primary actor** | Broker |
| **Secondary actor(s)** | Insurer (offline); System; Client (notified) |
| **Pre-conditions** | Claim is "Under Assessment" with an assessor appointed. The Insurer has completed its assessment and advised the Broker of the outcome by email. The assessment report itself is retained by the Insurer and is not provided to the Broker. |
| **Trigger** | Broker receives the Insurer's decision. |

**Business rules**

1. Decision must be one of three defined outcomes: Settled, Repudiated, or Within Excess.
2. If "Settled", a settlement method (Cash/Repair/Replacement) must be recorded.
3. If "Repudiated", a Repudiation Reason is mandatory and must be captured before the record can be saved.
4. If "Within Excess", the system records that the loss falls within or below the policy excess and that no
   Insurer liability arises.
5. The system must timestamp the decision for audit purposes.

**Success end condition** — Claim status is updated to reflect the decision (and the Repudiation Reason,
where applicable); Client is automatically notified.

**Failed end condition** — Decision not captured, an incorrect outcome selected, or a "Repudiated" outcome
saved without a reason — the system blocks saving until a Repudiation Reason is entered; any correction
thereafter is via an audit-logged edit by an authorised user only.

**Steps** — Broker opens the relevant claim → selects the outcome: Settled (Cash/Repair/Replacement),
Repudiated (declined by the Insurer), or Within Excess → if "Repudiated", Broker enters the mandatory
Repudiation Reason; if "Settled", Broker enters supporting notes/amount → system updates the claim status
and timestamps the change (audit trail) → system notifies the Client of the outcome (and reason, where
applicable) by outbound email.

---

## UC-06 — Manage Agreement of Loss (AOL) Cycle

| Field | Value |
|---|---|
| **Linked FRs** | FR-12 |
| **Primary actor** | Broker (issuing) and Client (signing) |
| **Secondary actor(s)** | Insurer (offline); System |
| **Pre-conditions** | Claim outcome recorded as "Settled" (UC-05). |
| **Trigger** | Insurer has issued an Agreement of Loss/authorisation to the Broker (via email). |

**Business rules**

1. The document must be uploaded before status can progress to "Awaiting Signed AOL".
2. Only a signed copy satisfies this stage.
3. The system must retain both the unsigned and signed versions.

**Success end condition** — Signed AOL is stored against the claim; status progresses to "Awaiting Insurer
Payment".

**Failed end condition** — Client disputes the AOL amount or does not sign; Broker liaises offline and
status remains "Awaiting Signed AOL", flagged for follow-up.

**Steps** — Broker uploads the Agreement of Loss/authorisation document to the claim → system notifies the
Client that a document requires action → Client downloads the document and signs it (electronically, or by
printing and signing) → Client uploads the signed copy to the portal → system marks the item "Received" and
notifies the Broker → Broker forwards the signed AOL to the Insurer via email (Phase 1, outside the
system).

---

## UC-07 — Close Claim

| Field | Value |
|---|---|
| **Linked FRs** | FR-12, FR-13 |
| **Primary actor** | Broker / Administrator |
| **Secondary actor(s)** | System; Client |
| **Pre-conditions** | Claim decision recorded (UC-05): Settled, Repudiated, or Within Excess. For Settled claims: signed AOL received (where cash settlement) and Proof of Payment received from the Insurer via email. For Repudiated and Within Excess claims: the Client has been notified of the outcome and reason (where applicable). |
| **Trigger** | For Settled claims, the Broker uploads Proof of Payment and selects "Close Claim"; for Repudiated and Within Excess claims, the Broker selects "Close Claim" once the Client has been notified. |

**Business rules**

1. Only the Broker (own clients) or Administrator/Manager (any client) may close a claim.
2. Once closed, the claim becomes view-only to Client users.
3. Only an Administrator may re-open/amend a closed claim.
4. Closed claims must be retained for a minimum of 5 years (FSCA), regardless of outcome.

**Success end condition** — Claim status = "Closed"; record becomes view-only; retained per retention
policy.

**Failed end condition** — An attempt to close a Settled claim without Proof of Payment on file is rejected
with a validation message; an attempt to close a Repudiated claim without a Repudiation Reason on file is
likewise refused.

**Steps** — For a Settled claim: Broker uploads the Proof of Payment received from the Insurer. For a
Repudiated or Within Excess claim: Broker confirms the Client has been notified of the outcome → Broker
selects "Close Claim" → system validates that all mandatory closure documents/fields for the relevant
outcome are present → system sets claim status to "Closed" and locks the record to view-only for Client
users → system notifies the Client that the claim has been closed → claim remains retrievable (view-only,
with full audit history) for a minimum of 5 years.

---

## UC-08 — Generate & Download Reports

| Field | Value |
|---|---|
| **Linked FRs** | FR-14, FR-15, FR-16 |
| **Primary actor** | Client / Broker / Manager |
| **Secondary actor(s)** | System |
| **Pre-conditions** | User is authenticated with appropriate access. Underlying claim data exists for the selected client/period. |
| **Trigger** | User selects a report type (and optionally a date range/client) from the Reports menu. |

**Business rules**

1. Client users may only generate reports for their own organisation.
2. Broker users may only generate reports for their assigned clients.
3. Manager/Administrator may generate reports for any client.
4. Report layout must reproduce the Client-supplied template exactly, as specified in the BRD's Appendix H
   (FR-32).

**Success end condition** — Workbook is generated in downloadable spreadsheet format reflecting current
data, and is stored against the client record (FR-28).

**Failed end condition** — No data available for the selected criteria — system displays an empty-state
message rather than an error.

**Steps** — User navigates to "Reports" → selects the report type (Claims History Report / Performance
Report) → selects the reporting period — defaulting to the current underwriting year — and the report
scope: consolidated across all of the client's policies, or a single named policy (FR-22) → system compiles
the workbook from current claim/policy data → system offers the workbook for download and stores the
generated instance against the client record (FR-28) → user downloads and saves the workbook.

---

## UC-09 — Maintain Client, Policy & Asset Records

| Field | Value |
|---|---|
| **Linked FRs** | FR-09, FR-10, FR-21 |
| **Primary actor** | Administrator / Broker |
| **Secondary actor(s)** | System; Manager |
| **Pre-conditions** | A new SOE client has been onboarded. Policy summary information is available. |
| **Trigger** | A new client/policy/asset is onboarded, or an existing policy or asset list is renewed/amended. |

**Business rules**

1. Only an Administrator (or authorised Broker) may create/edit client, policy and asset records.
2. A client cannot be linked to a claim until at least one active policy record exists.
3. Where a product requires asset-level claiming (e.g. Motor Fleet), at least one asset must be registered
   under the relevant policy/section before a claim can be lodged against it.
4. Policy documents uploaded are not necessarily visible to all user roles (least privilege).
5. Maximum of 2 active users (Primary/Secondary) per Client organisation.

**Success end condition** — Client, policy, and (where applicable) asset record(s) are created/updated and
available for selection during claim submission.

**Failed end condition** — Mandatory policy or asset fields are missing — record is saved as
"Incomplete/draft" and cannot be selected for claims until completed.

**Steps** — Administrator/Broker selects "New Client", "New Policy", or "New Asset" → captures the client
name, assigned broker, and contact users (up to 2 — Primary/Secondary) → captures the policy number, period,
insurer(s), class of insurance, section(s), sums insured/limit of indemnity, excess(es) and
terms/conditions → where applicable, registers the individual assets under the policy/section (e.g. each
vehicle in a fleet, each insured building) → uploads the policy schedule/summary document → system saves
the record(s) and makes them available for claim submission and reporting.

---

## UC-10 — Manage User Accounts & Access Rights

| Field | Value |
|---|---|
| **Linked FRs** | FR-17 |
| **Primary actor** | Administrator |
| **Secondary actor(s)** | System |
| **Pre-conditions** | Administrator is authenticated with elevated privileges. |
| **Trigger** | A new employee/client contact requires access, a role changes, or access must be revoked (e.g. staff exit). |

**Business rules**

1. Least-privilege principle applies.
2. MFA is mandatory for all roles.
3. Maximum of 2 active users per Client organisation (Primary/Secondary).
4. Broker users see only their assigned clients; Manager/Administrator see all clients.

**Success end condition** — User account is created/updated with the correct role and access scope; MFA
enrolment is enforced on first login.

**Failed end condition** — An attempt to exceed the 2-user limit per client, or assign conflicting roles, is
rejected with a validation message.

**Steps** — Administrator selects "New User" (or edits an existing user) → captures user details and
assigns a role (Administrator, Manager, Broker/User, Client-Primary, Client-Secondary) → system validates
role rules (e.g. client user limit) → system sends the user an invitation to set up login credentials and
enrol in MFA → user's access scope is applied per the [Access Rights Matrix](user-roles.md) → the action is
recorded in the audit trail.

---

## UC-11 — System Flags Late-Reported Claim

| Field | Value |
|---|---|
| **Linked FRs** | FR-19 |
| **Primary actor** | System |
| **Secondary actor(s)** | Client; Broker |
| **Pre-conditions** | Client is submitting/has submitted a claim (UC-01). |
| **Trigger** | Claim submission, or an edit where the date of loss is captured/changed. |

**Business rules**

1. 30-day threshold is measured from date of loss to date of registration on the portal.
2. The claim is flagged, not blocked.
3. The flag is visible to Broker and Client with an explanatory message that the Insurer may reject the
   claim.

**Success end condition** — Claim is saved with a "Late Reported" flag and warning message displayed;
Broker is notified.

**Failed end condition** — N/A — validation is advisory, not blocking. A failure would be the system not
applying the flag when the threshold is breached, treated as a system defect.

**Steps** — Client (or Broker capturing on their behalf) submits the date of loss and date of registration →
system calculates the difference in days → if greater than 30 days, system sets a "Late Reported" flag on
the claim → system displays a warning message to the Client and notifies the Broker → claim proceeds through
the normal workflow with the flag visible at every stage.

---

## UC-12 — Client-Broker Communication on a Claim

| Field | Value |
|---|---|
| **Linked FRs** | FR-08, FR-20 |
| **Primary actor** | Client / Broker |
| **Secondary actor(s)** | System |
| **Pre-conditions** | Claim exists and both parties are authenticated. |
| **Trigger** | Either party requires an update or needs to communicate information relevant to the claim. |

**Business rules**

1. Messages are timestamped, attributed to the sending user, and retained as part of the claim's audit
   trail.
2. Where outbound email integration is enabled, Broker-originated messages are also delivered to the
   Client's external email inbox.

**Success end condition** — Message is stored against the claim and visible to both parties (and delivered
to the Client's email inbox, where applicable).

**Failed end condition** — Message fails to send (system/connectivity issue); user is notified to retry.

**Steps** — User (Client or Broker) opens the claim and selects "Add Message/Note" → user types the
message/request and submits → system timestamps and stores the message against the claim → system notifies
the recipient by outbound email → recipient views and responds; the exchange builds a retained communication
log.

---

## UC-13 — Receive Automated Reminder

| Field | Value |
|---|---|
| **Linked FRs** | FR-07 |
| **Primary actor** | System |
| **Secondary actor(s)** | Client; Broker |
| **Pre-conditions** | A claim has an outstanding checklist item, or has had no status change for a configured period. |
| **Trigger** | Scheduled reminder job / configured inactivity threshold reached. |

**Business rules**

1. Reminder frequency/threshold is configurable by the Administrator.
2. Reminders are sent by outbound email only; in-app and SMS notification are out of scope.
3. Reminders continue until the outstanding item is resolved or the claim status changes.

**Success end condition** — Responsible party (Client or Broker) receives a reminder referencing the
specific outstanding item/claim.

**Failed end condition** — Reminder fails to deliver (e.g. invalid contact details); logged for
Administrator follow-up.

**Steps** — System evaluates open claims against configured reminder rules on a scheduled basis → system
identifies claims with outstanding checklist items or no recent status change → system generates a reminder
message identifying the specific outstanding item → system delivers the reminder by outbound email to the
responsible party → reminder is logged against the claim.

---

## UC-14 — View Audit Trail

| Field | Value |
|---|---|
| **Linked FRs** | FR-18 |
| **Primary actor** | Administrator / Manager |
| **Secondary actor(s)** | System |
| **Pre-conditions** | User is authenticated with audit-trail viewing permission. |
| **Trigger** | User needs to investigate a discrepancy, respond to a client/insurer query, or perform a compliance review. |

**Business rules**

1. Audit entries are immutable (cannot be edited or deleted by any user).
2. Audit trail must be retained for a minimum of 5 years (FSCA).
3. Only Administrator/Manager may view the full audit trail; Brokers may view the trail limited to their
   own actions/claims.

**Success end condition** — User views a chronological, attributable log of all actions taken against the
record.

**Failed end condition** — N/A (view-only); a failure would be missing/incomplete log entries, treated as a
system defect.

**Steps** — Authorised user opens the claim or client/policy record → user selects "Audit Trail"/"History"
→ system displays a chronological log: action, user, timestamp, and (where applicable) before/after values →
user may filter by date range or action type → user exports or references the log as required (e.g. for a
compliance review).

---

## UC-15 — Broker/Administrator Lodges Claim on Behalf of Client

| Field | Value |
|---|---|
| **Linked FRs** | FR-01, FR-33 |
| **Primary actor** | Broker (or Administrator) |
| **Secondary actor(s)** | System (Claims Portal); Client-Primary (notified) |
| **Pre-conditions** | Broker/Administrator is registered and authenticated (MFA). The Broker is assigned to the Client on whose behalf the claim is being lodged (Administrator may act for any Client); at least one active policy (and, where applicable, at least one registered asset) is loaded against the Client's profile. |
| **Trigger** | A Client reports a loss/incident to the Broker by phone, email, or in person, and is unable or unwilling to lodge the claim via the portal themselves. |

**Business rules**

1. The Broker may only lodge claims for Clients assigned to them (FR-10); the Administrator may lodge for
   any Client.
2. The system records the lodging user (name, role) and the lodgement channel ("Broker-Assisted") on the
   claim record, distinct from the Client the claim is lodged for.
3. The Client-Primary contact is notified automatically once the claim is lodged; no Client confirmation is
   required before the claim proceeds (FR-33). All other lodgement rules in UC-01 (30-day reporting flag,
   policy/section/asset selection, system-generated claim number) apply equally.

**Success end condition** — Claim record is created with a unique in-house claim number, status =
"Submitted", lodgement channel = "Broker-Assisted", document checklist generated, and the Client-Primary
contact notified.

**Failed end condition** — Claim is not saved due to incomplete mandatory fields, the Broker attempting to
lodge for a Client not assigned to them, session timeout, or system error.

**Steps** — Broker/Administrator logs into the portal (MFA) → selects "New Claim" and selects the Client on
whose behalf the claim is being lodged (limited to the Broker's assigned Clients; unrestricted for
Administrator) → selects the relevant policy and section, and (where applicable) the specific asset being
claimed against → enters loss details (date of loss, location, narrative, claim type) on the Client's behalf
→ system validates mandatory fields and checks the date of loss against the 30-day rule → system generates a
unique in-house claim reference number → system generates the required-document checklist based on claim
type/product → system sets claim status to "Submitted", records the lodgement channel as "Broker-Assisted",
and logs the lodging user against the claim → system automatically notifies the Client-Primary contact by
outbound email that a claim has been lodged on their behalf, with a summary and a link to view it → Broker/
Administrator receives on-screen confirmation and the claim reference number.
