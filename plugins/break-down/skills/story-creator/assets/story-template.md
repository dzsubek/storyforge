# Story template

Default shape for one story, enabler, or spike. Use the project's template when it has one; otherwise map these sections onto the destination's fields (for example title, body, labels). Keep every section; write "none" rather than deleting a heading, so a reader knows it was considered.

```markdown
# STORY-<n>: <Outcome, from the beneficiary's point of view>

**Type:** Story | Enabler | Spike
**Parent:** EPIC-<n>
**Readiness:** ready | needs clarification | needs discovery | blocked (<reason / F-ids>)
**Splitting pattern:** <pattern that produced this story>
**Timebox:** <Spikes only>
**Priority:** <only if the project or source gives a basis>   **Estimate:** <only if requested; provisional, with a one-line rationale>

## Narrative
Story: As a <beneficiary>, I want <capability> so that <outcome>.
Enabler: Provides <capability> required by <STORY-ids or REQ constraint>.
Spike: Question: <...>. Evidence to gather: <...>. Timebox: <...>. Decision informed: <...>.

## Context
<Minimum background to implement without the planning conversation: why this exists, the requirements it implements (verbatim or close, with locators), glossary terms, decisions and assumptions that constrain it. Prefer IDs and links to long copies.>

| REQ | Requirement | Modality | Source |
| REQ-006 | Members can download annual statements as PDF | can | DOC-1 §3 ¶1 |
| REQ-015 | Sessions time out after 30 minutes of inactivity | (must) | DOC-1 §5 ¶1 |
<Modality is one of must, must not, should, should not, may, or can, in parentheses when inferred. Never the sentence's verb or a provenance label.>

## Scope
**In:** <bullets>
**Out:** <bullets; especially adjacent behaviour a reader might assume is included, and which story covers it>

## Acceptance criteria
AC-1 (REQ-xxx) Given <...>, when <...>, then <...>.
AC-2 (REQ-xxx) ...

**Optional improvements (no source requirement; accept or reject):**
- ...

## Implementation notes
<Only grounded guidance. Name modules, endpoints, and paths you have verified in the repository or design; mark anything unverified `proposed`. Describe logical components when the codebase was not available. Nothing here overrides the project's Definition of Done.>

## Dependencies
| Depends on | Reason | Blocks |
| ENABLER-1 | needs member region to route | implementation |

**Shared resources:** <schemas, migrations, contracts, fixtures, environments touched by other stories too>

## Open questions and assumptions
<F-ids affecting this story, one line each.>

## Verification evidence
<How completion will be demonstrated: automated tests to add, manual demo steps, metrics or logs to inspect.>
```
