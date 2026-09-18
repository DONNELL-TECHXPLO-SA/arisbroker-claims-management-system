# Traceability Matrix

> Source: TechXplo – Claims Management Portal BRD v0.7 (17 Sep 2026), Section 10. Consolidates the
> traceability referenced throughout [`functional-requirements.md`](functional-requirements.md),
> [`use-cases.md`](use-cases.md), and [`../07-testing/test-cases.md`](../07-testing/test-cases.md) into a
> single view for review and audit purposes, cross-referencing every Functional Requirement to the Use
> Case(s) that realise it and the Test Case(s) that verify it.

| FR ID | Requirement | Linked Use Case(s) | Linked Test Case(s) |
|---|---|---|---|
| FR-01 | Online Claim Registration | UC-01, UC-15 | TC-01, TC-15 |
| FR-02 | Automated In-House Claim Reference Number | UC-01 | TC-01 |
| FR-03 | Required-Document Checklist | UC-01, UC-02 | TC-01, TC-02 |
| FR-04 | Document Management (Upload / Store / Retrieve) | UC-02 | TC-02 |
| FR-05 | Claims Tracking & Status Management | UC-03, UC-04 | TC-03, TC-04 |
| FR-06 | Insurer Claim Number Linking | UC-04 | TC-04 |
| FR-07 | Automated Reminders & Notifications | UC-13 | TC-13 |
| FR-08 | Client-Broker Communication Log | UC-12 | TC-12 |
| FR-09 | Client & Policy Management | UC-09 | TC-09 |
| FR-10 | Broker-Client Allocation | UC-09 | TC-09 |
| FR-11 | Assessor Recording | UC-04 | TC-04 |
| FR-12 | Settlement & Agreement of Loss (AOL) Workflow | UC-05, UC-06, UC-07 | TC-05, TC-06, TC-07 |
| FR-13 | Claim Closure & Archival | UC-07 | TC-07 |
| FR-14 | Reporting — Claims History Report | UC-08 | TC-08 |
| FR-15 | Reporting — Performance Report | UC-08 | TC-08 |
| FR-16 | Reporting — Claim-Loss Ratio Tab | UC-08 | TC-08 |
| FR-17 | User Role & Access Management | UC-10 | TC-10 |
| FR-18 | Audit Trail | UC-14 | TC-14 |
| FR-19 | Late-Reporting Validation (30-Day Rule) | UC-11 | TC-11 |
| FR-20 | Outbound Email Integration (Outlook) | UC-12 | TC-12 |
| FR-21 | Asset Register | UC-01, UC-09 | TC-01, TC-09 |
| FR-22 | Report Generation Engine | UC-08 | TC-08 |
| FR-23 | Underwriting Year Derivation | UC-08 | TC-08 |
| FR-24 | Risk Premium & Payment Frequency Capture | UC-09 | TC-09 |
| FR-25 | Claim Financial Values | UC-05, UC-08 | TC-05, TC-08 |
| FR-26 | Motor Claim Detail Capture | UC-01, UC-09 | TC-01, TC-09 |
| FR-27 | Separate Broker and Client Comment Streams | UC-08, UC-12 | TC-08, TC-12 |
| FR-28 | Generated Report Persistence | UC-08 | TC-08 |
| FR-29 | Report Header, Branding and Legend | UC-08 | TC-08 |
| FR-30 | Progress Classification | UC-08 | TC-08 |
| FR-31 | Company & Report Settings | UC-08, UC-10 | TC-08, TC-10 |
| FR-32 | Report Layout Fidelity | UC-08 | TC-08 |
| FR-33 | Broker/Administrator-Assisted Claim Lodgement | UC-15 | TC-15 |

## How to Read This

- Every row's **Requirement** is defined in full in [`functional-requirements.md`](functional-requirements.md).
- Every **Use Case** is defined in full in [`use-cases.md`](use-cases.md), including pre-conditions,
  business rules, and step-by-step scenarios.
- Every **Test Case** is defined in full in [`../07-testing/test-cases.md`](../07-testing/test-cases.md),
  including reproduction steps and expected behaviour. As at BRD v0.7, all test cases are recorded as **Not
  Executed**, pending build completion and formal UAT.
