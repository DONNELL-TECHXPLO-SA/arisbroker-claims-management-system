# User Roles

> Source: TechXplo – Claims Management Portal BRD v0.7 (17 Sep 2026), Sections 12.5, 12.9, 15 ("User
> Access / Access Rights Table"), FR-17, UC-09, UC-10.

The system supports **five** user roles, governed by the principle of least privilege (NFR-02) and mandatory
MFA (NFR-01). Indicative volumes: ~12 broker-side accounts (1 Administrator, 1 Manager, ~10 Broker/Users);
up to 2 accounts per SOE Client (~14–15 clients × up to 2 = ~28–30 client-side accounts).

## Access Rights Matrix

Legend: **F** = Full (Create/Read/Update/Delete) · **E** = Edit (Create/Read/Update) · **V** = View only ·
**O** = Restricted to own scope (assigned clients / own organisation) · **N** = No access.

| Module / Function | Administrator | Manager | Broker (User) | Client – Primary | Client – Secondary |
|---|---|---|---|---|---|
| Claim Registration (new claim) | F | F | O (own clients) | O (own org) | O (own org) |
| Claims Tracking / Status View | F (all) | F (all) | O (own clients) | O (own claims) | O (own claims) |
| Document Upload / Download | F | F | O (own clients) | E (own claims) | E (own claims) |
| Claim Status / Decision Update | F | F | O (own clients) | N | N |
| Claim Closure | F (all clients) | F (all clients) | O (own clients) | N | N |
| Reopen / Edit Closed Claim | F | V | N | N (comments only) | N (comments only) |
| Client & Policy Management | F | F | E (own clients, view others) | V (own org) | V (own org) |
| User & Access Management | F | O (view own team) | N | N | N |
| Reporting (Claims History / Performance) | F (all clients) | F (all clients) | O (own clients) | O (own org) | O (own org) |
| Communication Log | V (all) | V (all) | E (own clients) | E (own claims) | E (own claims) |
| Audit Trail | V (all) | V (all) | O (own actions/claims) | N | N |
| Assessor Detail Capture | F | F | O (own clients) | V | V |
| Company & Report Settings | F | N | N | N | N |

## Role: Administrator

| Field | Value |
|---|---|
| **Description** | Aris Brokers internal user with full system control. Proposed Phase 1 incumbent: David. |
| **Access level** | Full access across all modules and all clients/brokers; the only role with access to Company & Report Settings. |

**Can do**

- Create/edit/deactivate any user account and assign roles (FR-17).
- Create/edit any Client, Policy, Insurer and Asset record (UC-09).
- View, action, and close claims for any client (not limited to own assigned clients).
- Reopen/amend a closed claim (the only role permitted to do so — FR-13).
- Maintain the Product & Document Configuration reference data (add/amend products and document
  requirements without a development release).
- Maintain Company & Report Settings (FR-31).
- Generate reports for any client.
- View the full audit trail across all brokers/clients.

**Cannot do**

- Nothing is restricted for this role within Phase 1 scope, other than actions reserved to other actors
  outside the system entirely (e.g. assessor appointment, which is the Insurer's function).

**Key workflows**

- Manage user accounts & access rights (UC-10).
- Maintain client, policy & asset records (UC-09).
- Close a claim / reopen a closed claim (UC-07).
- Maintain Company & Report Settings.

---

## Role: Manager

| Field | Value |
|---|---|
| **Description** | Aris Brokers internal oversight user, sees all claims/clients across all brokers for management and reporting purposes. |
| **Access level** | Full visibility and action rights across all clients' claims; view-only on the audit trail and on reopening closed claims; no user-management or company-settings access. |

**Can do**

- View and action claims for any client across all brokers (used when a claim's assigned Broker is
  unavailable — FR-10).
- Close claims for any client.
- Create/edit Client & Policy records.
- View (but not edit) other brokers' user accounts ("view own team").
- Generate reports for any client.

**Cannot do**

- Create, edit or deactivate user accounts (Administrator only).
- Reopen a closed claim (view-only).
- Access Company & Report Settings.

**Key workflows**

- Generate & download reports (UC-08).
- Pick up / reassign a claim when the assigned Broker is unavailable.

---

## Role: Broker (User)

| Field | Value |
|---|---|
| **Description** | Aris Brokers claims handler responsible for day-to-day claim processing for their assigned SOE clients (~10 additional users). |
| **Access level** | Restricted to their own assigned clients ("own scope") across all modules. |

**Can do**

- Register a claim on behalf of an assigned Client (UC-15) or process a Client-submitted claim (UC-04).
- Upload/manage documents, update claim status, record the Insurer's decision, manage the AOL cycle, and
  close claims — for assigned clients only (FR-10).
- Edit Client & Policy records for assigned clients; view others read-only.
- Capture assessor details.
- Generate reports for assigned clients only.
- View the audit trail limited to their own actions/claims.

**Cannot do**

- View or action claims/clients not assigned to them.
- Manage user accounts.
- Reopen a closed claim.
- Access Company & Report Settings.

**Key workflows**

- Submit new claim / lodge on behalf of Client (UC-01, UC-15).
- Process & forward claim to Insurer (UC-04).
- Record Insurer decision (UC-05); manage AOL cycle (UC-06); close claim (UC-07).
- Client-Broker communication (UC-12).

---

## Role: Client – Primary

| Field | Value |
|---|---|
| **Description** | The first of up to 2 permitted contacts for an SOE client organisation. |
| **Access level** | Restricted to their own organisation's claims and policies ("own org" / "own claims"). |

**Can do**

- Submit a new claim (UC-01) and upload supporting documents (UC-02).
- Track claim status via a personal dashboard (UC-03).
- Communicate with the assigned Broker on a claim (UC-12).
- Upload a signed Agreement of Loss / proof of payment where required (UC-06).
- Leave a comment on a claim, including after closure (FR-27).
- Generate reports for their own organisation.
- Receive automated reminders and the Broker-Assisted-lodgement confirmation email (FR-33).

**Cannot do**

- View another client organisation's claims, policies, or reports.
- Update claim status/decision, close a claim, or edit closed-claim data (comments only).
- Manage users or view the audit trail.

**Key workflows**

- Submit new claim (UC-01); upload supporting documents (UC-02); track claim status (UC-03);
  client-broker communication (UC-12); generate & download reports (UC-08).

---

## Role: Client – Secondary

| Field | Value |
|---|---|
| **Description** | The second (optional) of up to 2 permitted contacts for an SOE client organisation, e.g. the manager of the Primary contact. |
| **Access level** | Identical scope and permissions to Client – Primary, for the same organisation. |

**Can do / Cannot do / Key workflows** — identical to Client – Primary above. The distinction between
Primary and Secondary is administrative (e.g. the Primary contact is the fixed addressee for generated
reports and the Broker-Assisted-lodgement notification per FR-29/FR-33), not a difference in system
permissions.

---

## Notes & Open Items

- The maximum of 2 active users per Client organisation is a confirmed business rule (FR-17, UC-10); edge
  cases for larger SOE clients needing more than 2 stakeholders are tracked as an open item — see
  [`open-questions.md`](open-questions.md).
- A "responsible claims handler" field for internal continuity/succession planning (beyond the fixed
  Broker-to-Client assignment in FR-10) is also an open item.
