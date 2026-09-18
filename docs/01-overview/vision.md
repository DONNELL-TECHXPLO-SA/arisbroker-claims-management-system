# Vision

> Source: TechXplo – Claims Management Portal BRD v0.7 (17 Sep 2026), Sections 1–2.

## Problem Statement

Aris Brokers ("the Client") is a South African short-term insurance broker operating exclusively in the
commercial lines space, servicing State-Owned Enterprise (SOE) clients. The Client currently manages its
entire claims lifecycle manually:

- Correspondence with both the insured client and the insurer is conducted via email (and occasionally
  telephone/WhatsApp for initial reporting).
- Supporting documentation is filed in a document repository ("Lucid", functioning similarly to Microsoft
  OneDrive).
- This creates challenges around:
  - **Visibility** — clients cannot see real-time claim progress.
  - **Traceability** — there is no consolidated, auditable trail of claim activity.
  - **Scalability** — the Client's own SOE clients are increasingly requiring their broker to operate a
    dedicated digital claims platform.

TechXplo has been engaged to design and build a **Claims Management Portal** that digitises this process
end-to-end for Phase 1, while broker-to-insurer correspondence remains on email in the interim (see
[`scope.md`](scope.md)).

## Target Users

See [`stakeholders.md`](stakeholders.md) for the full stakeholder list and
[`../02-requirements/user-roles.md`](../02-requirements/user-roles.md) for the detailed system roles and
permissions. In summary, the system serves:

- **Aris Brokers internal users** — an Administrator, a Manager, and ~10 additional Broker/Claims Handler
  users.
- **Client (SOE) users** — up to 2 users per client organisation (Primary and Secondary contacts) across
  ~14–15 onboarded SOE clients.

## Why This System Exists (Objectives)

The primary objective is to deliver a secure, web-based Claims Management Portal that digitises and
automates the Client's end-to-end commercial-lines claims process, from claim lodgement by the insured
through to insurer settlement and claim closure. Specific objectives confirmed by the Client are to:

1. **Improve operational efficiency** by reducing reliance on manual, email-based claims handling.
2. **Increase transparency for clients** by providing real-time, self-service claim status visibility —
   replacing the current experience where clients may see no change in status for weeks at a time.
3. **Strengthen regulatory compliance** with the Financial Sector Conduct Authority (FSCA) through a
   defensible, timestamped audit trail, retained for a minimum of five years.
4. **Support client retention and competitiveness** — several SOE clients have indicated they require
   their broker to operate its own digital claims platform.
5. **Centralise documents and correspondence** per claim, reducing dependence on scattered email threads
   and the existing Lucid/OneDrive-style repository.
6. **Enable accurate, self-service reporting** for clients (the Claims History Report and the Performance
   Report) without manual compilation by the Client's staff.
7. **Position the Client for further digital transformation** in future phases (e.g. Microsoft 365
   integration, potential insurer-side integration).

## Traceability Model

The BRD (and this documentation set) structures requirements in three linked layers so that traceability
is maintained from business need through to test evidence:

- **Functional Requirements** ([`../02-requirements/functional-requirements.md`](../02-requirements/functional-requirements.md))
  — the "what" the system must do.
- **Use Cases** ([`../02-requirements/use-cases.md`](../02-requirements/use-cases.md)) — the "how" a
  specific actor interacts with the system to satisfy one or more Functional Requirements.
- **Test Cases** ([`../07-testing/test-cases.md`](../07-testing/test-cases.md)) — the verification steps
  used to confirm a Use Case (and its underlying Functional Requirement(s)) has been correctly built.

A single [`traceability-matrix.md`](../02-requirements/traceability-matrix.md) cross-references every
Functional Requirement to the Use Case(s) and Test Case(s) that realise and verify it.

## Note on Scope of This Document Set

Per the current phase of work, this documentation captures **what the system needs to do** (requirements)
and **where each requirement lives** in the existing project structure. It deliberately does not make
technical architecture, database, or API design decisions — those follow in a later phase once these
requirements have been reviewed (see [`../03-architecture/README.md`](../03-architecture/README.md) and
[`../04-design/README.md`](../04-design/README.md), which remain unpopulated for now).
