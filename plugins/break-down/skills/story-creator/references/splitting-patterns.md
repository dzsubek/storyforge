# Splitting patterns

Read this when an epic does not split obviously, when a story is still too big after the first pass, or when non-functional requirements are hard to place. Each pattern has a guardrail because applied mechanically it can drop an obligation.

## The meta-pattern

1. Find the core of complexity: the thing that, if it did not work, would make the whole epic pointless.
2. Extract one simplest complete path through that core as the first story: the narrowest data, the single most common rule, the plainest interface, still end-to-end and demonstrable.
3. Treat everything else as variations around the core and split along the dimension that varies.
4. After each split, apply the four-question test: Does it create a prioritisation option? Does it isolate lower-value work so it can be dropped or delayed? Does it reduce uncertainty? Does every child still deliver a demonstrable outcome? If low-value complexity is still smeared across every child, try a different dimension.

## Pattern menu

| Pattern | Split along | Example | Guardrail |
|---|---|---|---|
| Simplest complete path / journey steps | One realistic journey first, then alternative steps | Checkout with one payment method, then add saved cards, then add vouchers | Each step must be usable alone; do not create fragments that only make sense together |
| Business-rule variations | One policy first, then others | Shipping to domestic addresses, then international, then restricted goods | Mandatory restrictions (prohibitions, compliance) stay in every applicable slice |
| Data variations | One input format or entity type, then others | Import CSV, then XLSX; one currency, then many | Required validation and integrity rules are retained in the first slice |
| User roles | One beneficiary first | Members view statements, then admins view on behalf of members | Do not defer the authorisation the first role already needs |
| Operations | Read, create, update, delete when each has standalone value | View profile, then edit profile | "CRUD" labels alone do not prove value; a "delete" with no user need is not a story |
| Interface / channel | One channel, then others | Web first, then email notification, then mobile push | Label mocks and stubs; retain required accessibility |
| Simple before complex | Basic usable interaction, then richer | Plain list, then sortable, then filterable | Required usability constraints are not "richness" and stay in the first slice |
| Integrations | One provider, then others | One identity provider first | Say when the first slice uses a mock; do not claim real integration delivery |
| Scale and optimisation | Approved initial load first, optional optimisation later | Correct results first, then caching | Never defer a **required** performance, reliability, or security target; deferring it creates a `deferred` disposition that needs approval |
| Discovery (Spike) | Separate a blocking unknown | "Can the legacy API return statements older than 5 years?" | A spike produces evidence and a decision, with a timebox; it is not production acceptance |
| Micro-slice armature (optional, for AI executors or when the project asks for very small items) | Hard-coded data, then in-memory, then persisted | Price shown from a constant, then from a table, then from the database | Each armature slice is still end-to-end demonstrable |

## Placing non-functional requirements

- If the NFR constrains one specific behaviour, write it as an acceptance criterion on that story ("statement download completes within 3 s for a 5 MB file").
- If it is cross-cutting and substantial (audit logging on every action, a session timeout, encryption at rest), create an Enabler that names every story it serves, and reference it from those stories' dependencies.
- In both cases it must be measurable and traced to its REQ. "Secure" and "scalable" are not acceptance criteria.

## Enablers and spikes

An **Enabler** exists because stories need it, not because an architecture diagram has a box. Write it as the capability it provides and list the stories or constraints it serves. If you cannot name a served story or a source constraint, question whether it belongs in this epic.

A **Spike** has four parts: the question, the evidence to collect, the timebox, and the decision it informs. Stories waiting on it are `needs discovery`.

## Signs you have split wrong

- Children are layers (API, UI, database) or phases (design, build, test).
- A child cannot be demonstrated to a stakeholder without another child shipping first, and that is not a declared prerequisite.
- Every child carries the same hard complexity; the split did not isolate anything.
- A child's acceptance criteria are a subset of another child's.
- The split produced a dozen two-line stories for an epic a developer could finish in a week. Merge.
