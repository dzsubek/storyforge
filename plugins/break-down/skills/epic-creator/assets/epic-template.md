# Epic template

Default shape for one epic. Use the project's own template when it has one; otherwise adapt field names to the destination (for example, a tracker's title/description/labels) while keeping every section below present. Omit a section only when it is genuinely empty, and say so ("Dependencies: none identified").

```markdown
# EPIC-<n>: <Capability name, as an outcome>

**Type:** Epic | Enabler | Spike
**Readiness:** ready | needs decision (<F-ids>; breakdown can begin) | blocked (<F-ids>; core cannot proceed)
**Priority:** <only if the source or project gives a basis; otherwise omit>

## Outcome
<One or two sentences: who benefits and what becomes possible or true when this is delivered. For an Enabler, name the epics or constraints it serves. For a Spike, name the question, the timebox, and the decision it will inform.>

## Scope
**Includes:** <bullets, in the source's terms>
**Excludes:** <bullets; especially anything a reader might assume is included>

## Requirements covered
| REQ | Requirement (short) | Modality | Source |
| REQ-012 | ... | must | DOC-1 §3.2 |
| REQ-013 | ... | (must) | DOC-1 §3.2 |
<Modality is copied unchanged from the ledger: must, must not, should, should not, may, or can, in parentheses when inferred. Never the sentence's verb.>

## Constraints and non-functional requirements
<NFRs, compliance, or policy obligations that bind this epic, each with its REQ ID. Write "none stated in source" if that is the case.>

## Dependencies
| Depends on | Reason | Blocks | 
| EPIC-2 | needs the permission model to decide who may export | implementation |

**Shared resources:** <data models, contracts, environments this epic touches that other epics also touch>

## Open questions and assumptions
<F-ids from the findings register that affect this epic, with one line each.>

## Glossary terms
<Terms from the project glossary that a reader of this epic needs.>
```
