# Break Down

Break a specification into epics, then into implementation-ready stories, without dropping or inventing requirements.

## Skills

- **epic-creator** (`/break-down:epic-creator`). Decompose a spec, PRD, or requirements document into epics with source traceability. When the input is too small to decompose, it organizes the content into a structured brief.
- **story-creator** (`/break-down:story-creator`). Split one epic, feature, or brief into user stories with acceptance criteria, scope boundaries, typed dependencies, and traceability back to the source requirements.

Use epic-creator on a whole specification. Use story-creator on one epic or on a brief that epic-creator produced.

Output destination and format follow the project's `AGENTS.md` or `CLAUDE.md`. Neither skill assumes a tracker.

## How it works

Decomposition runs in two levels. Each level is extract-first, group-second, and read-only until you approve publishing.

1. **Inventory and extract.** Read the source, map its structure, and pull every atomic obligation into a requirements ledger with modality, actors, and a precise source locator. Nothing is grouped yet.
2. **Reconcile.** Merge duplicates, build a glossary, and surface contradictions, ambiguities, missing information, and consequential questions. Low-impact gaps become recorded assumptions; consequential ones block the affected work.
3. **Structure at the right grain.** Epic-creator decides whether the ledger needs several epics or a single structured brief. Story-creator takes one epic or brief and slices it into stories, enablers, and spikes.
4. **Attach quality and dependencies.** Non-functional requirements bind where they apply. Cross-item dependencies are typed (design, implementation, integration, release) rather than implied.
5. **Check coverage, then present.** Every requirement must map to work or carry an explicit disposition (`question`, `deferred`, `excluded`). You get a summary and findings register first; nothing is written to a tracker or the repo until you approve.

## Decomposition principles

- **Outcomes over document structure.** Headings and authorship are grouping hypotheses, not scope. Epics and stories are coherent capabilities or demonstrable paths, not layers ("Backend") or phases ("Testing").
- **Traceability over paraphrase.** Requirements stay close to the source wording in the ledger. Modalities (`must`, `should`, `may`, `must not`) are preserved; suggestions are never upgraded to obligations.
- **Coverage over completeness theater.** 100% coverage means every requirement is accounted for, including deferred and excluded ones — not that everything is planned as ready work. Do not invent requirements or manufacture epics for a one-capability input.
- **Vertical slices over horizontal tasks.** The first story is the thinnest complete path that delivers the outcome end-to-end. Further splits follow what varies (rules, data, roles, channels, integrations), and each child must still be demonstrable.
- **Honest work types.** Technical prerequisites are Enablers that name the stories they serve. Unknowns that block credible planning are timeboxed Spikes. Neither is disguised as an "As a system…" user story.
- **Findings stay visible.** Ambiguities, contradictions, assumptions, and unreadable input live in a findings register tied to the work they affect — not silently resolved inside a summary.
