# Structured brief template

Use when the input is too small to decompose into epics (Step 5). The brief carries the same traceability and findings as a decomposition so that story-creator can consume it directly.

```markdown
# Brief: <capability name, as an outcome>

## Summary
<Two to four sentences: who benefits, what becomes possible, why it matters, in the source's terms.>

## Sources
| ID | Document | Version / date | Notes |

## Actors
<Each actor or system role named in the source, with its definition if given.>

## Requirements
| ID | Requirement | Type | Modality | Actors | Source | Status |

## Constraints and non-functional requirements
<Each with its REQ ID; "none stated" if applicable.>

## Scope boundaries
**Includes:** ...
**Excludes / not mentioned:** <things a reader might assume; mark which are stated exclusions and which are simply absent>

## Dependencies and prerequisites
<Other systems, decisions, or work the source implies must exist first.>

## Findings register
| # | Category | Finding | Source | Affects | Proposed handling |

## Glossary
| Term | Definition | Source | Synonyms |

## Next step
This brief is a single epic-sized unit. Use story-creator to split it into stories once the consequential findings above are resolved.
```
