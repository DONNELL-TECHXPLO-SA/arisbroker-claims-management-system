# Test Cases

> Source: TechXplo – Claims Management Portal BRD v0.7 (17 Sep 2026), Section 9 ("Test Case Scenarios").
> Each test case verifies a [Use Case](../02-requirements/use-cases.md) and its underlying
> [Functional Requirement(s)](../02-requirements/functional-requirements.md) — see
> [`../02-requirements/traceability-matrix.md`](../02-requirements/traceability-matrix.md) for the
> consolidated cross-reference.
>
> **Status as at BRD v0.7**: every test case below is recorded as **Not Executed**, pending build
> completion and formal User Acceptance Testing (UAT). Step-level Actual Results and Pass/Fail columns are
> intentionally omitted here (they belong in a live test-execution record, e.g. a test-management tool or
> UAT sign-off log) — only the planned steps and expected results are captured, per this repo's convention
> of not duplicating time-sensitive execution state in a static requirements doc.
>
> **ID convention note**: kept as BRD-native TC-01–TC-15 for direct traceability to the source BRD.

---

### TC-01 — Linked to UC-01 / FR-01, FR-02, FR-03, FR-26

**Description**: Submit a new claim and verify the auto-generated claim number and document checklist.

| Step | Step Details | Expected Result |
|---|---|---|
| 1 | Log in with valid Client MFA credentials. | Login succeeds and the dashboard loads. |
| 2 | Select "New Claim" and choose a policy/section. | Claim form opens with the policy pre-populated. |
| 3 | Enter valid loss details (date within 30 days) and submit. | Claim is saved; unique claim number generated; checklist displayed; status = Submitted. |
| 4 | Check the Broker's claims queue. | Broker sees a new-claim notification. |

---

### TC-02 — Linked to UC-02 / FR-03, FR-04

**Description**: Upload a supporting document against an outstanding checklist item.

| Step | Step Details | Expected Result |
|---|---|---|
| 1 | Open a claim with an outstanding checklist item. | Checklist is visible with outstanding items highlighted. |
| 2 | Select the outstanding item and upload a valid file. | Upload progress is shown, followed by a success confirmation. |
| 3 | Refresh the claim view. | Item is marked "Received"; document is viewable/downloadable. |
| 4 | Check the audit trail. | Entry is logged with user, timestamp, and action = "Document Uploaded". |

---

### TC-03 — Linked to UC-03 / FR-05

**Description**: Client views real-time claim status after a Broker-side update.

| Step | Step Details | Expected Result |
|---|---|---|
| 1 | Broker updates claim status to "Under Assessment" (setup step). | Status change is saved. |
| 2 | Client logs in and opens "My Claims". | Claim list shows the updated status. |
| 3 | Client opens the specific claim. | Full timeline/history displayed, matching the Broker's update. |

---

### TC-04 — Linked to UC-04 / FR-06, FR-11

**Description**: Broker captures the Insurer claim number and the 3-field assessor detail.

| Step | Step Details | Expected Result |
|---|---|---|
| 1 | Attempt to progress the claim without an Insurer claim number. | System blocks progression with a validation message. |
| 2 | Enter the Insurer claim number and save. | Status updates to "Submitted to Insurer". |
| 3 | Enter the 3 assessor fields (Name/Company, Contact Details, Date Appointed) and save. | All 3 fields are stored against the claim and visible on the claim detail; no other assessor functionality is present. |
| 4 | Verify the Client notification. | Client sees the status change on their dashboard. |

---

### TC-05 — Linked to UC-05 / FR-12, FR-25

**Description**: Broker records the Insurer's decision (Settled / Repudiated / Within Excess), including the
mandatory Repudiation Reason.

| Step | Step Details | Expected Result |
|---|---|---|
| 1 | Select "Settled – Cash", enter the amount, save. | Status = "Settled – Cash"; Client is notified. |
| 2 | Select "Within Excess", save. | Status = "Within Excess"; Client is notified that the loss falls within or below the excess. |
| 3 | Select "Repudiated" and attempt to save without a reason. | System blocks saving and prompts for a mandatory Repudiation Reason. |
| 4 | Enter a Repudiation Reason and save. | Status = "Repudiated"; Client is notified with the reason. |

---

### TC-06 — Linked to UC-06 / FR-12

**Description**: Agreement of Loss upload / sign / re-upload cycle.

| Step | Step Details | Expected Result |
|---|---|---|
| 1 | Broker uploads the unsigned AOL. | Client is notified; document is available for download. |
| 2 | Client downloads and uploads the signed copy. | Signed version is stored; checklist item marked "Received". |
| 3 | Verify both versions are retrievable. | Unsigned and signed versions are both present in the document history. |

---

### TC-07 — Linked to UC-07 / FR-12, FR-13

**Description**: Close a claim under each of the three outcomes (Settled, Repudiated, Within Excess).

| Step | Step Details | Expected Result |
|---|---|---|
| 1 | Attempt "Close Claim" on a Settled claim without Proof of Payment uploaded. | System rejects the action with a validation message. |
| 2 | Upload Proof of Payment and select "Close Claim". | Status = Closed; record becomes view-only for Client users. |
| 3 | Select "Close Claim" on a Within Excess claim (Client already notified). | Status = Closed; record becomes view-only. |
| 4 | Attempt to close a Repudiated claim with no Repudiation Reason on file. | System rejects the action with a validation message; once a reason is captured, closure succeeds. |

---

### TC-08 — Linked to UC-08 / FR-14, FR-15, FR-16, FR-22, FR-23, FR-25, FR-27, FR-28, FR-29, FR-30, FR-31, FR-32

**Description**: Generate and download each of the two report types.

| Step | Step Details | Expected Result |
|---|---|---|
| 1 | Generate the Claims History Report for a client. | All claims for that client are listed with current status. |
| 2 | Generate the Performance Report for a specific policy. | Claim amount, excess, and recoveries/salvage are reflected. |
| 3 | Generate the Claim-Loss Ratio tab data. | Correct percentage of claims vs. premium is calculated. |
| 4 | Attempt as a Broker user for a non-assigned client. | Access is denied / client is not selectable. |

---

### TC-09 — Linked to UC-09 / FR-09, FR-10, FR-21, FR-24, FR-26

**Description**: Create a client, policy and asset record, including the 2-user limit.

| Step | Step Details | Expected Result |
|---|---|---|
| 1 | Administrator creates a new client with 2 contact users. | Record saved successfully. |
| 2 | Attempt to add a 3rd client contact. | System blocks the action with a validation message. |
| 3 | Capture policy details and upload the schedule. | Policy is saved and available in the claim-submission dropdown. |
| 4 | Register an individual asset (e.g. a fleet vehicle) under the policy. | Asset is saved and selectable when a claim is lodged against that policy/section. |

---

### TC-10 — Linked to UC-10 / FR-17, FR-31

**Description**: User provisioning and access-scope enforcement.

| Step | Step Details | Expected Result |
|---|---|---|
| 1 | Admin creates a Broker user and assigns 2 SOE clients. | User is created, scoped to those 2 clients only. |
| 2 | New user logs in for the first time. | Forced MFA enrolment before dashboard access is granted. |
| 3 | Broker user attempts to view a non-assigned client. | Access is denied. |
| 4 | Manager logs in. | All clients across all brokers are visible. |

---

### TC-11 — Linked to UC-11 / FR-19

**Description**: Late-reported claim flag (30-day rule).

| Step | Step Details | Expected Result |
|---|---|---|
| 1 | Submit a claim with date of loss 45 days prior. | Claim is saved with a "Late Reported" flag and warning message. |
| 2 | Submit another claim with date of loss 10 days prior. | Claim is saved with no late flag. |
| 3 | Verify the Broker notification for the late claim. | Broker sees a late-flag indicator on the claims queue. |

---

### TC-12 — Linked to UC-12 / FR-08, FR-20, FR-27

**Description**: Client-Broker in-portal communication.

| Step | Step Details | Expected Result |
|---|---|---|
| 1 | Client adds a message requesting an update. | Message is saved and timestamped; Broker is notified. |
| 2 | Broker responds via the portal. | Response is saved; Client is notified by outbound email. |
| 3 | Review the claim's communication log. | Full, chronological, attributed message history is displayed. |

---

### TC-13 — Linked to UC-13 / FR-07

**Description**: Automated reminder for an outstanding item.

| Step | Step Details | Expected Result |
|---|---|---|
| 1 | Leave a claim with an outstanding document (e.g. 5 business days). | Reminder job identifies the claim. |
| 2 | Reminder is delivered to the responsible party. | The outbound email references the specific outstanding item. |
| 3 | Party resolves the outstanding item. | Reminder cycle stops for that item. |

---

### TC-14 — Linked to UC-14 / FR-18

**Description**: View the audit trail for a claim and confirm immutability.

| Step | Step Details | Expected Result |
|---|---|---|
| 1 | Manager opens a claim and selects "Audit Trail". | Chronological log of all actions, with user and timestamp, is displayed. |
| 2 | Attempt to edit/delete a log entry (any role). | System blocks the action; no edit/delete option is available. |
| 3 | Broker opens the audit trail on their own assigned claim. | Broker sees their own actions/claim history (scoped view). |

---

### TC-15 — Linked to UC-15 / FR-01, FR-33

**Description**: Broker lodges a new claim on behalf of a Client and verifies the Client-Primary contact is
notified.

| Step | Step Details | Expected Result |
|---|---|---|
| 1 | Log in as a Broker-User assigned to a test Client (MFA). | Login succeeds and the Broker dashboard loads. |
| 2 | Select "New Claim", select the test Client, and choose a policy/section. | Claim form opens; only the Broker's assigned Clients are selectable. |
| 3 | Enter valid loss details (date within 30 days) and submit. | Claim is saved; unique claim number generated; lodgement channel = "Broker-Assisted"; checklist displayed; status = Submitted. |
| 4 | Check the Client-Primary contact's email inbox. | Client-Primary has received an email confirming the claim was lodged on their behalf, with a summary and a link to view it. |
