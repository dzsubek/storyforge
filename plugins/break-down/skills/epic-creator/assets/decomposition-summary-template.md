# Decomposition summary template

Present this to the user before publishing anything, and include it (or its equivalent in the project's format) alongside the epics as the traceability record.

```markdown
# Decomposition of <document title(s)>

## Sources
| ID | Document | Version / date | Notes |
| DOC-1 | Payments PRD | v2.3, 2026-03-01 | §6 diagrams unreadable |

## Epics
| ID | Epic | Type | REQs covered | Readiness |
| EPIC-1 | Shoppers filter and sort the catalogue | Epic | REQ-001..009 | ready |
| EPIC-2 | Admin permission model | Enabler | REQ-010..014 | blocked on F-3 |

## Coverage
- Requirements extracted: <n> (<n> documented, <n> derived)
- Covered by at least one epic: <n>
- Requirements with disposition `question`: <n>
- Deferred: <n> (each needs approval; listed below)
- Excluded: <n> (each needs approval; listed below)
- Consequential findings awaiting a decision: <n> (see findings register)

Dispositions needing approval:
| REQ | Disposition | Reason |

## Dependencies and suggested order
<Edges from the epics, plus a short ordering rationale. Priority, if given, is listed separately from order.>

## Findings register
| # | Category | Finding | Source | Affects | Proposed handling |

## Glossary
| Term | Definition | Source | Synonyms |

## Requirement ledger
<Full ledger table, or a link to the working file if it was written separately.>
```
