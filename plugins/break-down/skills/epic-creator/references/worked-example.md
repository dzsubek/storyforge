# Worked example

A deliberately small, invented specification run through the workflow, to show the expected level of detail. Real inputs are longer; the shape stays the same.

## Input (DOC-1, "Member Portal PRD v0.4")

> §1 Goals. Members should be able to manage their own account without contacting support.
> §2 Account. Members can update their email and postal address. Email changes require confirmation via a link sent to the new address; the link expires after 24 hours. Members must not be able to change the email to one already used by another account.
> §3 Documents. Members can download their annual statements as PDF. Statements for the last 7 years must be available. Downloads should be fast.
> §4 Support. Members can open a support ticket from the portal. Tickets are routed to the regional team. Agents can reply; members see replies in the portal and by email.
> §5 Security. All portal actions require an authenticated session. Sessions time out after 30 minutes of inactivity. Admins can view any member's account (read only).
> §6 Reporting. TBD.

## Step 3: Ledger (excerpt)

| ID | Requirement | Type | Modality | Actors | Source | Status |
|---|---|---|---|---|---|---|
| REQ-001 | Members can update their email address | functional | can | member | DOC-1 §2 ¶1 | documented |
| REQ-002 | Members can update their postal address | functional | can | member | DOC-1 §2 ¶1 | documented |
| REQ-003 | Email change requires confirmation via link sent to the new address | functional | must | member, system | DOC-1 §2 ¶1 | documented; relates_to REQ-001 |
| REQ-004 | Confirmation link expires after 24 hours | constraint | (must) | system | DOC-1 §2 ¶1 | documented; relates_to REQ-003 |
| REQ-005 | Member must not change email to one used by another account | constraint | must not | member | DOC-1 §2 ¶2 | documented |
| REQ-006 | Members can download annual statements as PDF | functional | can | member | DOC-1 §3 ¶1 | documented |
| REQ-007 | Statements for the last 7 years must be available | data | must | system | DOC-1 §3 ¶1 | documented |
| REQ-008 | Downloads should be fast | NFR | should | system | DOC-1 §3 ¶1 | documented; flag: unquantified |
| REQ-009 | Members can open a support ticket from the portal | functional | can | member | DOC-1 §4 ¶1 | documented |
| REQ-010 | Tickets are routed to the regional team | functional | (must) | system | DOC-1 §4 ¶1 | documented; flag: "regional" undefined |
| REQ-011 | Agents can reply to tickets | functional | can | agent | DOC-1 §4 ¶1 | documented |
| REQ-012 | Members see replies in the portal | functional | (must) | member | DOC-1 §4 ¶1 | documented |
| REQ-013 | Members receive replies by email | functional | (must) | member | DOC-1 §4 ¶1 | documented; relates_to REQ-012 |
| REQ-014 | All portal actions require an authenticated session | constraint | must | all | DOC-1 §5 ¶1 | documented |
| REQ-015 | Sessions time out after 30 minutes of inactivity | constraint | (must) | system | DOC-1 §5 ¶1 | documented |
| REQ-016 | Admins can view any member's account, read only | functional | can | admin | DOC-1 §5 ¶1 | documented |
| REQ-017 | Reporting requirements | — | — | — | DOC-1 §6 | question |
| REQ-018 | A member's region is known when a ticket is routed | data | (must) | system | derived from REQ-010 | derived |

Note what was kept: the 24-hour and 7-year quantities, the prohibition in REQ-005, the split of REQ-012/013, the derived REQ-018 with its parent, and the flags on REQ-008 and REQ-010.

Note the Modality column. It holds only the six keywords. REQ-005, REQ-007 and REQ-008 carry the source's own word. REQ-003 and REQ-014 say "require", which states the obligation outright, so they are `must` without parentheses. REQ-004, REQ-010, REQ-012, REQ-013 and REQ-015 are plain statements of behaviour ("expires", "are routed", "see", "time out"), so the strength is inferred and written `(must)`; the verb itself never goes in the column. REQ-018 is derived, so it is inferred too. REQ-017 has no statement yet and gets `—`.

## Step 4: Reconciliation findings

| # | Category | Finding | Source | Affects | Proposed handling |
|---|---|---|---|---|---|
| F-1 | Missing information | §6 Reporting is "TBD" | DOC-1 §6 | REQ-017 | Ask; excluded from this decomposition pending input |
| F-2 | Ambiguity | "Downloads should be fast" has no target | DOC-1 §3 | EPIC-2 | Ask for a target (low impact; a provisional p95 can be assumed if confirmed) |
| F-3 | Ambiguity | "Regional team" and how a member's region is determined are undefined | DOC-1 §4 | EPIC-3 | Ask (consequential: changes routing behaviour) |
| F-4 | Missing information | Admin read-only view: which admins, and is access audited? | DOC-1 §5 | EPIC-4 | Ask (consequential: permission boundary) |
| F-5 | Dependency | Every epic requires authenticated sessions with 30-min timeout | DOC-1 §5 | EPIC-4 → EPIC-1..3 | Sequence EPIC-4 first or confirm an existing auth system satisfies REQ-014/015 |
| F-6 | Assumption | "Member" means a logged-in account holder; "agent" means a support staff user | glossary absent | all | Recorded; reversible |

## Step 5 decision

Four distinct outcomes (self-service account changes, statement access, support conversations, authenticated access with admin oversight) with three actors. Decompose.

## Step 6: Epics (summary form)

| ID | Epic | Type | REQs covered | Readiness |
|---|---|---|---|---|
| EPIC-1 | Members update their own contact details safely | Epic | 001–005 | ready |
| EPIC-2 | Members access seven years of annual statements | Epic | 006–008 | ready (F-2 open, low impact) |
| EPIC-3 | Members and agents converse through support tickets | Epic | 009–013, 018 | blocked on F-3 |
| EPIC-4 | Authenticated portal access with admin read-only oversight | Enabler | 014–016 | blocked on F-4 |

Observe that §2 and §5 were not copied as epics one-to-one: the session rules in §5 became an Enabler because every other epic depends on them and no member "wants" a timeout, while the admin view in §5 is grouped with it because it shares the permission model. REQ-017 has a `question` disposition rather than being silently dropped.

## Coverage

- Extracted: 18. Covered: 17. Question: 1 (REQ-017). Deferred: 0. Excluded: 0.

## What the one-epic case would look like

If the input had been only §2, the correct deliverable is the structured brief: the §2 ledger rows, actors (member, system), constraints (REQ-004, REQ-005), scope boundaries (postal address format not specified), findings (none consequential), and a note that it is ready for story-creator. No epics.
