# Acceptance criteria

Read this when writing criteria for stories that have business rules, edge cases, or quality obligations. Acceptance criteria are the story's real boundary; a vague title with precise criteria is implementable, the reverse is not.

## Method: Example Mapping

For each story list three things before writing any criterion:

1. **Rules**: the business rules the story must honour, each traced to a REQ.
2. **Examples**: concrete cases that illustrate each rule, including the unhappy ones.
3. **Questions**: things you cannot answer from the sources.

Examples become criteria. Questions go to the findings register; they do not become criteria that guess at an answer.

## Format choice

- **Given / When / Then** when behaviour depends on state and events. One scenario per criterion; several criteria per story is normal.
- **Checklist** for structural or operational deliverables: "a reversible migration adds `region` to `members`", "the dashboard panel shows ticket volume by region for the selected week".
- Identify each criterion (`AC-1`, `AC-2`) and link it to its REQ: `AC-3 (REQ-004)`.

## Review dimensions

Select only those relevant to the story; do not turn every row into new scope.

| Dimension | Ask |
|---|---|
| Main behaviour | What does the beneficiary see or get when everything goes right? |
| Rules and permissions | Who may do this? What is forbidden? What happens on a forbidden attempt? |
| Data cases | Empty, invalid, duplicate, boundary (exactly at the limit, one over), unavailable |
| State and time | Transitions, retries, timeouts, concurrent actions, partial failure, idempotency |
| Quality obligations | Accessibility, privacy, security, performance, compatibility, only where a REQ or project policy requires them |
| Evidence | How will completion be shown? Test, demo, log, screenshot, metric? |

## Mandatory versus optional

A criterion is **mandatory** when it traces to a REQ or an approved project policy (Definition of Done, security baseline). Anything you add from the dimensions above without such a trace is an **optional improvement**; list it in a separate block so reviewers can accept or reject it. This keeps the checklist from silently growing the epic.

## Good and poor criteria

Poor: "The export should be fast and handle errors gracefully."
Why: not observable, no actor, no threshold, two concerns in one.

Good:
```
AC-1 (REQ-006) Given a member with at least one statement, when they request the 2024 statement, then a PDF for 2024 downloads and its content matches the statement record.
AC-2 (REQ-007) Given a member with statements from 2017 to 2024, when they open the statements page, then all eight years are listed and none older than seven years is shown.
AC-3 (REQ-008, provisional target F-2) Given a 5 MB statement, when the member downloads it, then the download starts within 3 seconds at the p95 on the reference environment.
AC-4 (REQ-014) Given no authenticated session, when a statement URL is requested directly, then the response is a redirect to login and no file content is returned.
```

Poor: "User can log in."
Good:
```
AC-1 (REQ-021) Given a registered member with a correct password, when they submit the login form, then they land on their account page and a session is created.
AC-2 (REQ-022) Given a registered member, when they enter a wrong password five times within 15 minutes, then the sixth attempt is rejected with a lockout message and no further attempts are evaluated for 15 minutes.
AC-3 (REQ-023) Given any login attempt, when it fails, then the error message does not indicate whether the email exists.
```

## Checks before finalising

- Every criterion is observable and binary.
- Every mandatory criterion has a REQ or policy link; optional ones are in their own block.
- The union of the story's criteria describes a demonstrable outcome, not a partial layer.
- No criterion answers a question that is still open in the findings register.
- Quantities and modalities match the requirement exactly.
