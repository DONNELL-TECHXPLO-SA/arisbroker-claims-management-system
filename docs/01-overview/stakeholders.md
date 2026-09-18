# Stakeholders

> Source: TechXplo – Claims Management Portal BRD v0.7 (17 Sep 2026), Sections 1 and 5.

## BRD Distribution List / Project Stakeholders

| Name | Role | Organisation | Responsibility |
|---|---|---|---|
| Sam Mabasa | Project Manager | TechXplo | Requirements gathering, BRD authoring, client liaison |
| Eugene Ndlovu | Business Analyst / Technical Analyst | TechXplo | Requirements gathering, technical clarification |
| Donnell Naidoo | Lead Software Developer | TechXplo | Solution design & development; independent BRD reviewer |
| Naison | Claims Manager (primary contact) | Aris Brokers | Primary business contact; process owner; business sign-off |
| David | Proposed System Administrator | Aris Brokers | Platform administration once live |
| Claims handlers / brokers (~10 additional) | Broker Users | Aris Brokers | Day-to-day claim processing for assigned SOE clients |
| SOE client contacts (e.g. City Power — example only) | Client Users (Primary / Secondary) | The Client's own SOE clients | Claim submission, document upload, and status tracking |

Aris Brokers is referred to as "the Client" throughout the BRD and this documentation set. It is a South
African short-term insurance broker operating exclusively in the commercial lines space, servicing
State-Owned Enterprise (SOE) clients.

## Decision Ownership

- **Business/process sign-off**: Naison (Claims Manager, Aris Brokers).
- **Solution design & development**: Donnell Naidoo (TechXplo).
- **Requirements clarification & scope changes**: Sam Mabasa / Eugene Ndlovu (TechXplo) jointly with the
  Client, tracked via [`../02-requirements/open-questions.md`](../02-requirements/open-questions.md).
- **Formal BRD acceptance / authorisation to build**: recorded via the accompanying Scope of Work document,
  signed separately by Aris Brokers.

## System-Level Roles

The stakeholders above map to five system user roles (Administrator, Manager, Broker/User,
Client-Primary, Client-Secondary). See
[`../02-requirements/user-roles.md`](../02-requirements/user-roles.md) for the detailed permissions
matrix.
