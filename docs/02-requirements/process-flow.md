# System Process Flow

> Source: TechXplo – Claims Management Portal BRD v0.7 (17 Sep 2026), Section 14 (Figures 2 and 2a).
> Presents the desired (to-be) end-to-end claims process. Realises [`use-cases.md`](use-cases.md) UC-01
> through UC-15 as a single connected flow. The original BRD renders this as two diagrams (a detailed
> vertical flowchart, Figure 2, and a simplified swimlane overview, Figure 2a); both are transcribed below
> as structured text since this documentation set is Markdown-only at this stage.

## Actors

Four actors participate in the process: **Client** (insured SOE), **Broker** (via the Claims Portal),
**Insurer**, and **Assessor**. Actions performed inside the Claims Portal are noted as *(in-system)*;
actions performed outside the system (predominantly via email) are noted as *(offline/email)*.

## End-to-End Flow (Figure 2)

1. **Loss/incident occurs.**
2. **How is the claim lodged?**
   - **Client self-service**: Client logs in (MFA) and submits the claim — policy, section, asset, date and
     place of loss, narrative, claim type, photographs *(in-system)*. See UC-01.
   - **Broker-assisted**: Broker/Administrator lodges the claim on the Client's behalf *(in-system, FR-33,
     UC-15)*; system emails the Client-Primary contact to confirm a claim was lodged on their behalf
     *(FR-33)*.
3. **System** generates the in-house claim reference number and the required-document checklist *(FR-02,
   FR-03)*.
4. **Decision point — Reported within 30 days of loss?**
   - **No** → System flags "Late Reported"; Broker captures a written motivation. The claim is **not
     blocked** *(FR-19)*.
   - **Yes** → claim continues unflagged.
5. **Broker** notifies the Insurer at lodgement, by email *(offline/email)*.
6. **Insurer** registers the claim and allocates the Insurer claim number *(offline/email)*.
7. **Broker** captures the Insurer claim number on the portal; status → "Submitted to Insurer" *(FR-06,
   in-system)*. Client is notified by email *(FR-07, FR-20)*.
8. **Client** uploads supporting documents against the checklist. The checklist is advisory and does not
   block progress *(FR-03, FR-04, in-system)*.
9. **Decision point — Items still outstanding?**
   - **Yes** → reminder loop: system emails a reminder to the responsible party while any item remains
     outstanding *(FR-07, outbound email only, in-system)* → returns to step 8/9.
   - **No** → proceeds to step 10.
10. **Insurer** appoints an Assessor *(offline/email)*.
11. **Broker** captures the Assessor's name, contact, and date appointed; communicates the appointment to
    the Client *(FR-11, in-system)*.
12. **Assessor** assesses the claim and submits the report **to the Insurer** — the report is **not**
    provided to the Broker *(offline/email — outside system entirely)*.
13. **Insurer** decides the claim and advises the Broker by email *(offline/email)*.
14. **Broker** records the Insurer's decision on the portal; the system notifies the Client *(FR-12,
    in-system)*.
15. **Decision point — Decision outcome?**
    - **Repudiated (declined)** → Broker captures the mandatory Repudiation Reason and uploads the
      Insurer's repudiation letter; Client is notified → proceeds to closure check (step 19).
    - **Within Excess** → System notifies the Client that the loss falls within or below the policy excess;
      the Client bears the cost → proceeds to closure check (step 19).
    - **Settled** → proceeds to step 16 (settlement method).
16. **Decision point — Settlement method?**
    - **Cash** → Broker uploads the Agreement of Loss for the Client *(in-system)* → Client downloads,
      signs, and uploads the signed AOL *(in-system)* → **decision loop — Signed AOL received?** (No →
      reminder loop; Yes →) → Broker forwards the signed AOL to the Insurer *(offline/email)* → Insurer
      pays the claim and issues Proof of Payment; **no invoicing arises on a cash settlement**
      *(offline/email)* → Broker uploads the Proof of Payment and shares it with the Client *(in-system)*.
    - **Repair / Replacement** → Broker issues the excess invoice to the Client *(in-system)* → Client
      uploads proof of payment of the excess *(in-system)* → Broker records that the proof of payment has
      been forwarded to the service provider; **the system notifies the Client only** *(in-system)* →
      Insurer arranges the repair/replacement with the service provider *(offline/email — the system never
      corresponds with the service provider)*.
17. **Decision point — Closure requirements met for this outcome?**
    - **No** → System awaits the outstanding item; continues the reminder cycle *(FR-07)*.
    - **Yes** → Broker/Administrator closes the claim on the portal *(FR-13, in-system)*.
18. **Claim closed.** System makes the claim view-only to the Client; comments remain enabled (e.g. for
    subrogation or excess-refund notes); full audit trail retained for a minimum of five years (FSCA).

## Simplified Swimlane View (Figure 2a)

The BRD also presents an executive-summary swimlane version of the same flow, with each actor's actions
grouped into its own horizontal lane (Client, Broker, Insurer, Assessor). It omits the decision loops and
outside-system detail shown above and is intended for quick stakeholder reference — the numbered steps
above (Figure 2) remain the authoritative, complete version for build purposes.

## Claim Status Stages (Client-Facing)

To support real-time tracking (FR-05), the following status stages are **proposed** for the client-facing
dashboard. Exact wording is to be confirmed with the Client during design — see
[`open-questions.md`](open-questions.md) (Q-003):

1. Partially Submitted
2. Submitted
3. Documents Outstanding
4. Submitted to Insurer
5. Under Assessment
6. Assessment Completed
7. Awaiting Insurer Decision
8. Settled – cash, repair or replacement
9. Repudiated – declined by the Insurer, reason recorded
10. Within Excess – no Insurer liability, Client bears the cost
11. Disputed
12. Awaiting Signed Agreement of Loss
13. Awaiting Excess Invoice and Proof of Payment
14. Awaiting Insurer Payment
15. Not Taken Up (NTU)
16. Closed – comments enabled

## Architecture Note: Notifications & Reminders (Module 6)

Per the BRD's Section 4 module breakdown, the Notifications & Reminders capability is a **background
service, not a screen a user actively opens**. It monitors state changes in the claims and document data
(outstanding documents, an unsigned Agreement of Loss, a claim awaiting Insurer response) and triggers
reminders accordingly — these are the reminder loops described above. It has no delivery channel of its
own; it reuses the outbound Outlook email integration (FR-20). In-app and SMS channels are out of scope
(FR-07). The only place a user directly sees its output is as a prompt or banner within the Client
Dashboard.

*This "module" framing is the BRD's own logical grouping of related functionality (Section 4), provided as
context for the Functional Requirements above — it is not a technical architecture decision. Final
technical/module architecture will be confirmed separately (see [`../03-architecture/README.md`](../03-architecture/README.md)).*
