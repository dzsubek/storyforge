# Worked example

Continues the invented "Member Portal" example from epic-creator. Input is EPIC-2 plus its requirements; the project's `AGENTS.md` (invented) says stories are Markdown files under `backlog/stories/`, Given/When/Then criteria, no estimates.

## Input: EPIC-2 "Members access seven years of annual statements"

| REQ | Requirement | Modality | Source |
|---|---|---|---|
| REQ-006 | Members can download annual statements as PDF | can | DOC-1 §3 ¶1 |
| REQ-007 | Statements for the last 7 years must be available | must | DOC-1 §3 ¶1 |
| REQ-008 | Downloads should be fast | should | DOC-1 §3 ¶1; flag: unquantified (F-2) |
| REQ-014 | All portal actions require an authenticated session | must | DOC-1 §5 ¶1; via EPIC-4 |

Repository inspection (read-only) found `apps/portal/src/statements/` with a `StatementList` component and a `GET /api/statements` endpoint returning the current year only, and no archive integration.

## Step 3: Ambiguity routing

- F-2 ("fast" unquantified): low impact; record a provisional target and ask for confirmation. Does not block.
- New question F-7: where do statements older than the current year come from? The current API only serves one year, and the PRD does not say. Feasibility unknown, so this is a Spike, and stories needing older years are `needs discovery`.

## Step 4: Slicing

Core complexity: getting a statement PDF to an authenticated member. Simplest complete path: the current year's statement, which the existing endpoint already provides. Then split along data variation (older years) and surface the archive unknown as a spike. Session enforcement is already an Enabler in EPIC-4; stories declare it as a prerequisite rather than re-implementing it.

## Output: story set summary (excerpt)

| ID | Title | Type | REQs | Pattern | Readiness |
|---|---|---|---|---|---|
| STORY-1 | Member downloads the current year's statement as PDF | Story | REQ-006, REQ-008, REQ-014 | simplest complete path | ready |
| SPIKE-1 | Determine the source and access method for statements from previous years | Spike | REQ-007 | discovery | — |
| STORY-2 | Member sees and downloads statements for the last seven years | Story | REQ-006, REQ-007, REQ-008 | data variation | needs discovery (SPIKE-1) |

Coverage: 4 requirements in epic, 4 covered. Open questions: 1 (F-7). Deferred: 0.

Order: STORY-1 can start now (EPIC-4's enabler is already in place in the repository). SPIKE-1 in parallel. STORY-2 after SPIKE-1.

## Output: STORY-1

```markdown
# STORY-1: Member downloads the current year's statement as PDF

**Type:** Story   **Parent:** EPIC-2   **Readiness:** ready
**Splitting pattern:** simplest complete path

## Narrative
As a member, I want to download my current annual statement as a PDF so that I have an official record without contacting support.

## Context
The portal already lists the current year's statement (`StatementList`, backed by `GET /api/statements`) but offers no download. The PRD's goal (DOC-1 §1) is self-service without support contact. "Fast" in REQ-008 has no target; a provisional p95 of 3 s is assumed pending F-2.

| REQ | Requirement | Modality | Source |
| REQ-006 | Members can download annual statements as PDF | can | DOC-1 §3 ¶1 |
| REQ-008 | Downloads should be fast | should | DOC-1 §3 ¶1 |
| REQ-014 | All portal actions require an authenticated session | must | DOC-1 §5 ¶1 |

## Scope
**In:** download link for the current year's statement; PDF delivery; authenticated access.
**Out:** previous years (STORY-2); statement generation or formatting changes; email delivery (not in source).

## Acceptance criteria
AC-1 (REQ-006) Given an authenticated member with a current-year statement, when they choose "Download PDF", then a PDF for the current year downloads and its totals match the statement record shown on screen.
AC-2 (REQ-006) Given an authenticated member with no statement for the current year, when they open the statements page, then an explanatory empty state is shown and no download control appears.
AC-3 (REQ-014) Given no authenticated session, when the download URL is requested directly, then the response redirects to login and returns no file content.
AC-4 (REQ-008, provisional; F-2) Given a statement of up to 5 MB, when the member starts the download, then the first byte arrives within 3 s at the p95 on the reference environment.

**Optional improvements (no source requirement):**
- Show the file size next to the download control.

## Implementation notes
- Verified: `apps/portal/src/statements/StatementList.tsx` renders the list; `GET /api/statements` in `apps/api/src/statements/router.ts` returns current-year metadata.
- `proposed`: add `GET /api/statements/:year/pdf` guarded by the existing session middleware (`apps/api/src/auth/requireSession.ts`, verified) that streams the stored PDF. Whether PDFs are pre-generated or rendered on demand is not visible in the repository; confirm before choosing.

## Dependencies
| Depends on | Reason | Blocks |
| EPIC-4 session enforcement (existing middleware) | AC-3 relies on it | none (already present) |

**Shared resources:** `apps/api/src/statements/router.ts` (also touched by STORY-2).

## Open questions and assumptions
- F-2: performance target provisional at p95 3 s.

## Verification evidence
Automated API test for AC-1 and AC-3; component test for AC-2; a timing run against the reference environment recorded in the PR for AC-4.
```

## Output: SPIKE-1

```markdown
# SPIKE-1: Determine the source and access method for statements from previous years

**Type:** Spike   **Parent:** EPIC-2   **Timebox:** 1 day

Question: Where are statements older than the current year stored, and can the portal API retrieve them for a given member and year?
Evidence to gather: data owner for historical statements; storage location and format; retention actually available (REQ-007 requires 7 years); access latency.
Decision informed: whether STORY-2 reads from the existing API, a new archive integration, or a backfill, and whether REQ-007 is achievable as written.
Traces to: REQ-007 (DOC-1 §3 ¶1); finding F-7.
```

Note what the example shows: the first story is the thinnest end-to-end path and is already `ready`; the unknown became a spike rather than a guess; `proposed` marks the one unverified path; the unquantified NFR has a provisional, clearly labelled target; and nothing the epic promised is missing from the set.
