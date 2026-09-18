# Non-Functional Requirements

> Source: TechXplo – Claims Management Portal BRD v0.7 (17 Sep 2026), Section 11. Derived directly from
> the Client's stated priorities during the requirements sessions (security, performance,
> backup/recovery, and auditability), supplemented by standard practice for a regulated financial-services
> platform.
>
> **ID convention note**: kept as BRD-native NFR-01–NFR-12 for direct traceability to the source BRD (see
> the equivalent note in [`functional-requirements.md`](functional-requirements.md)). New NFRs beyond the
> BRD should continue from **NFR-13**.

---

### NFR-01: Security — Authentication

| Field | Value |
|---|---|
| **ID** | NFR-01 |
| **Category** | Security |
| **Status** | Approved |
| **Priority** | Must |

**Requirement** — All users must authenticate via multi-factor authentication (MFA) before accessing the
portal.

**Measurable target / metric** — No login is possible without a second verification factor (OTP/authenticator
app).

**Rationale** — Client-mandated security priority; protects commercial claims and client PII.

**Verification method** — TC-10 (UAT); security review at build sign-off.

---

### NFR-02: Security — Authorisation

| Field | Value |
|---|---|
| **ID** | NFR-02 |
| **Category** | Security |
| **Status** | Approved |
| **Priority** | Must |

**Requirement** — The system must enforce least-privilege, role-based access control across all modules.

**Measurable target / metric** — Users can only perform actions permitted by their assigned role — see
[`user-roles.md`](user-roles.md) (Access Matrix, BRD Section 15).

**Rationale** — Client-mandated; also underpins FSCA compliance and multi-tenant isolation between SOE
clients.

**Verification method** — TC-10; role-scoping tested per module in UAT.

---

### NFR-03: Data Protection & Privacy

| Field | Value |
|---|---|
| **ID** | NFR-03 |
| **Category** | Security |
| **Status** | Approved |
| **Priority** | Must |

**Requirement** — All data in transit and at rest must be encrypted; the system must support the broker's
POPIA obligations given the personal and commercial client data processed.

**Measurable target / metric** — TLS 1.2+ in transit; encryption at rest for the database and document
store.

**Rationale** — POPIA compliance; protects SOE client and claimant personal information.

**Verification method** — Security/architecture review during design phase; penetration test prior to
go-live (recommended).

---

### NFR-04: Performance — Document Volume

| Field | Value |
|---|---|
| **ID** | NFR-04 |
| **Category** | Performance |
| **Status** | Draft — **OPEN ITEM** |
| **Priority** | Must |

**Requirement** — The system must support a high volume of document uploads per claim without performance
degradation (volume, rather than size, was flagged as the primary driver by the Client).

**Measurable target / metric** — Target concurrent-user and document-volume benchmarks to be confirmed — see
[`open-questions.md`](open-questions.md).

**Rationale** — Claims commonly attract many supporting documents (photos, quotations, reports).

**Verification method** — Load testing against agreed benchmarks once confirmed.

---

### NFR-05: Scalability

| Field | Value |
|---|---|
| **ID** | NFR-05 |
| **Category** | Scalability |
| **Status** | Approved |
| **Priority** | Must |

**Requirement** — The system must scale to accommodate the initial ~14–15 clients, ~12 broker-side users
and up to ~30 client-side users, with headroom for client-base growth.

**Measurable target / metric** — Architecture supports horizontal scaling without redesign (design-phase
consideration).

**Rationale** — The Client expects its SOE client base to grow, and other clients are asking for a similar
platform.

**Verification method** — Architecture review during design phase.

---

### NFR-06: Availability

| Field | Value |
|---|---|
| **ID** | NFR-06 |
| **Category** | Availability |
| **Status** | Draft — **OPEN ITEM** |
| **Priority** | Must |

**Requirement** — The portal must be available during business hours as a minimum, with an agreed uptime
target. The Client has specifically requested visibility of system uptime.

**Measurable target / metric** — Uptime SLA to be agreed — see [`open-questions.md`](open-questions.md).
Uptime to be reported to the Client (e.g. via a status page or periodic report).

**Rationale** — Client-requested transparency and reliability given the transition away from email.

**Verification method** — Uptime monitoring/status reporting once SLA is agreed.

---

### NFR-07: Backup & Recovery

| Field | Value |
|---|---|
| **ID** | NFR-07 |
| **Category** | Availability |
| **Status** | Draft — **OPEN ITEM** |
| **Priority** | Must |

**Requirement** — Regular, automated backups of all claim, client and document data must be taken, with a
defined and tested disaster-recovery procedure.

**Measurable target / metric** — Backup frequency, RPO and RTO to be documented and agreed — see
[`open-questions.md`](open-questions.md).

**Rationale** — Protects irreplaceable claims/compliance data; supports the 5-year FSCA retention
requirement (NFR-09).

**Verification method** — DR test/restoration drill prior to go-live (recommended).

---

### NFR-08: Auditability

| Field | Value |
|---|---|
| **ID** | NFR-08 |
| **Category** | Security |
| **Status** | Approved |
| **Priority** | Must |

**Requirement** — All create/update actions on claims and client/policy records must be logged immutably.

**Measurable target / metric** — Audit entries cannot be edited or deleted by any role; retained for a
minimum of 5 years (FSCA).

**Rationale** — Directly required for FSCA compliance (Objective 3) and dispute resolution.

**Verification method** — TC-14 (UAT); confirm no edit/delete affordance exists for any role.

---

### NFR-09: Regulatory Compliance

| Field | Value |
|---|---|
| **ID** | NFR-09 |
| **Category** | Compliance |
| **Status** | Approved |
| **Priority** | Must |

**Requirement** — The system must support the broker's FSCA regulatory obligations, including minimum
5-year record retention.

**Measurable target / metric** — Retention policy enforced at the data layer; records are not purged before
the retention period lapses.

**Rationale** — The Client is regulated by the FSCA and must retain client and claims information for a
minimum of five years after policy cancellation (Section 3.4).

**Verification method** — Data retention policy review; confirm no hard-delete path exists for records
within the retention window.

---

### NFR-10: Usability

| Field | Value |
|---|---|
| **ID** | NFR-10 |
| **Category** | Usability |
| **Status** | Draft — **OPEN ITEM** |
| **Priority** | Should |

**Requirement** — The portal must be intuitive for non-technical commercial-client users (SOE claims
contacts) with minimal training required.

**Measurable target / metric** — To be validated through user acceptance testing / training feedback — see
[`open-questions.md`](open-questions.md).

**Rationale** — SOE client contacts have limited familiarity with self-service claims portals (see Risk
RSK-06 in [`../09-project-management/risk-register.md`](../09-project-management/risk-register.md)).

**Verification method** — UAT feedback; onboarding/training session outcomes.

---

### NFR-11: Notification Reliability

| Field | Value |
|---|---|
| **ID** | NFR-11 |
| **Category** | Maintainability |
| **Status** | Approved |
| **Priority** | Should |

**Requirement** — Automated reminders and notifications must be delivered reliably by outbound email.

**Measurable target / metric** — Delivery failures are logged and surfaced to the Administrator for
follow-up.

**Rationale** — Supports FR-07/FR-33 notification requirements; failure to notify undermines client trust
in the new process.

**Verification method** — TC-13; delivery-failure logging reviewed in UAT.

---

### NFR-12: Maintainability & Extensibility

| Field | Value |
|---|---|
| **ID** | NFR-12 |
| **Category** | Maintainability |
| **Status** | Approved |
| **Priority** | Should |

**Requirement** — The system should be built to allow Phase 2 enhancements (e.g. Microsoft 365 integration,
potential insurer-side integration) without major re-architecture.

**Measurable target / metric** — Modular, API-based architecture (a design-phase decision — see
[`../03-architecture/README.md`](../03-architecture/README.md)).

**Rationale** — Objective 7 (position the Client for further digital transformation); Section 3.4
(constraints — Phase 2 scoped separately).

**Verification method** — Architecture review during design phase.
