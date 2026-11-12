# Requirement extraction rules

Read this before extracting from documents longer than a few pages, documents with tables or appendices, or several documents that may overlap or conflict. The goal is a ledger faithful enough that every later decision (grouping, coverage, dependencies) can be checked against it.

## Contents

- [Read globally, extract locally](#read-globally-extract-locally)
- [What to preserve](#what-to-preserve)
- [Splitting compound statements](#splitting-compound-statements)
- [Status: documented, derived, question, recommendation](#status)
- [Tables, appendices, diagrams](#tables-appendices-diagrams)
- [Multiple documents and versions](#multiple-documents-and-versions)
- [Ambiguity lexicon](#ambiguity-lexicon)
- [Source locators](#source-locators)
- [Working file for long inputs](#working-file-for-long-inputs)

## Read globally, extract locally

1. Skim the whole input first to build the section map and spot cross-references ("see Appendix B", "as defined in §2"). Summaries written before the full read miss obligations buried in later sections.
2. Then extract section by section, appending to the one shared ledger. Keep the glossary and contradiction list open while you go; many conflicts only become visible when the second mention appears.
3. Revisit cross-references at the end: an appendix table frequently carries the only quantitative requirement in the document.

## What to preserve

Capture these as written, because each one changes what "done" means:

- **Actors**: who performs or receives the behaviour. If the sentence has no actor (passive voice), record `actor: unspecified` and flag it.
- **Conditions and triggers**: "when", "if", "unless", "after", "only for".
- **Quantities and units**: counts, limits, durations, percentages, currencies. Never round or convert.
- **Prohibitions**: "must not", "never", "is not permitted". These are the easiest to lose during regrouping.
- **Exceptions and fallbacks**: "except", "otherwise", "in case of failure".
- **Modality**: the strength of the commitment, recorded with the six keywords from SKILL.md ("Modality values"): `must`, `must not`, `should`, `should not`, `may`, `can`, parenthesised when inferred. A document's "should" is weaker than its "must"; do not normalise them. Edge cases:
  - "Will" and present-tense descriptions ("the system will send", "the system sends") carry no modal word. Both are `(must)`.
  - A table row or field list that states an obligation without a sentence ("Max length: 80") is `(must)`.
  - When a section heading or lead-in sets the strength for everything under it ("Nice to have:", "The following are mandatory:"), the items inherit it as an inferred value, `(should)` or `(must)`, because the word is not in the item's own sentence.
  - When two keywords fit one sentence, it holds two obligations. Split the row.

## Splitting compound statements

"Users can export reports as CSV or PDF and receive an email when the export completes" is three obligations: export as CSV, export as PDF, email on completion. Split them into separate rows and add `relates_to` links so the relationship (all three belong to one export flow) is not lost. Do not split a single condition from its behaviour; "if the export exceeds 10 MB it is delivered as a download link" is one requirement.

## Status

| Status | Use when | Required extra |
|---|---|---|
| `documented` | The text states it | — |
| `derived` | It is a necessary consequence that the text does not state (e.g., a stated audit log implies log storage and retention) | Rationale and parent REQ ID |
| `question` | The text leaves a consequential gap | Which decision is blocked |
| `recommendation` | The text suggests something optional ("consider", "ideally", "nice to have") | — |

Be sparing with `derived`. It exists so you can account for necessary work honestly, not so you can add scope. Anything you cannot trace to a parent REQ is an untraceable addition and must be flagged.

## Tables, appendices, diagrams

- Treat each table row that states an obligation as a requirement; use the row identifier in the locator.
- Appendices often hold field lists, error codes, limits, and compliance mappings. Extract them; they tend to be the most testable requirements in the document.
- For diagrams and images you cannot read, record what the caption claims and mark the content `unreadable` in the inventory. Do not reconstruct a diagram's content from surrounding prose as if you had seen it.

## Multiple documents and versions

- Assign each document its own ID and note the version or date.
- If two documents state the same requirement, keep one row and list both locators.
- If they conflict, record both as separate rows, mark both `conflict: C-n`, and add the conflict to the findings. Apply precedence only when the project or a document states which source wins. Recency is not precedence unless someone says it is.
- If a document is referenced but not supplied, record it in the inventory as missing and list the requirements that depend on it as `question`.

## Ambiguity lexicon

Flag rows containing these so the user can resolve them:

- Unbounded scope: "etc.", "and so on", "all", "any", "as needed", "where applicable".
- Placeholders: "TBD", "TBC", "to be defined", "[X]".
- Unquantified qualities: "fast", "quick", "responsive", "scalable", "secure", "user-friendly", "intuitive", "robust", "reasonable".
- Undefined actors: "the system", "users", "someone" without a role definition.
- Unanchored references: "the existing process", "the current API", "as before".

Flagging does not block extraction; extract what is stated and attach the flag.

## Source locators

A locator should let a reader find the sentence in under a minute. Prefer, in order of availability: `DOC-id §heading-path ¶n`, `DOC-id p.N`, `DOC-id Table T row R`, `DOC-id line N`. For chat or meeting transcripts use the speaker and timestamp or message index.

## Working file for long inputs

When the ledger exceeds what you can hold comfortably alongside the source, write it to a file as you go (one Markdown table, or a YAML or JSON list if the project prefers machine-readable artifacts). Location follows project convention; otherwise ask. Record at the top which sections have been processed. If you are interrupted, the file and the processed-range note are the deliverable; mark the result partial rather than presenting it as complete.
