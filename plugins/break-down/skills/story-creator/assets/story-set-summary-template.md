# Story set summary template

Present this to the user before publishing, and include it (or its equivalent in the project's format) with the stories as the traceability record for the epic.

```markdown
# Stories for EPIC-<n>: <epic name>

## Inputs
<Epic record, brief, documents, or conversation range used, with IDs or links.>

## Stories
| ID | Title | Type | REQs | Pattern | Readiness |
| STORY-1 | Member downloads one annual statement | Story | REQ-006 | simplest complete path | ready |
| STORY-2 | Member sees seven years of statements | Story | REQ-007 | data variation | ready |
| ENABLER-1 | Authenticated access to statement URLs | Enabler | REQ-014 | — | ready |
| SPIKE-1 | Can the archive serve statements older than 5 years? | Spike | REQ-007 | discovery | — |

## Coverage
- Requirements in epic: <n>
- Covered by at least one item: <n>
- Requirements with disposition `question`: <n>   Deferred: <n>   Excluded: <n>  (each deferral or exclusion listed below for approval)
- Items by readiness: ready <n> · needs clarification <n> · needs discovery <n> · blocked <n>
- Consequential findings awaiting a decision: <n> (see findings register)

| REQ | Disposition | Reason |

## Dependencies and suggested order
<Edges with type and reason; suggested order with rationale. Priority, if given, is separate.>

Parallel-safe groups (prerequisites met, no shared-resource conflict): <...>

## Findings register
| # | Category | Finding | Source | Affects | Proposed handling |

## Assumptions recorded
| # | Assumption | Affects | Reversible? |
```
