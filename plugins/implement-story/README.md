# Implement Story

Take one story-sized task from description to a reviewed, verified change on a local branch. A coordinator runs a fixed procedure. A planner writes a short plan, one implementer builds the change test-first, the coordinator runs the project's checks itself, and then independent verifiers check the result blind: a spec checker goes criterion by criterion, and five lens reviewers each look for one kind of defect. Serious findings go back to a fresh implementer for at most two fix rounds.

```bash
claude plugin install implement-story@dzsubek-hu-agent-mp
```

## Start a run

```bash
claude --agent implement-story:orchestrator "<task description, story link, or tracker ID>"
```

Add requirements for this run in the same prompt, such as "do not add dependencies" or "keep the public API unchanged". They are recorded word for word and bind every role. Options you can give in the prompt:

- **Dry run.** Stop after the plan.
- **Plan approval.** Always stop for approval at the plan gate.
- **Extra verifiers**, skipped lenses, or a different fix-round cap for this run.
- **Models per role**, such as "Use Sonnet for the implementer and Fable for the planner." Roles you do not mention keep the model in their definition (`inherit`, so the session's model). Also settable as `- Models: planner=fable, implementer=sonnet` in the project configuration below.

To resume an interrupted run, start the orchestrator again with no task, or with "resume".

## What happens

| Step | Who | Result |
|---|---|---|
| Intake | Orchestrator | `task.md`: the task word for word, with numbered acceptance criteria |
| Plan | Planner | `plan.md`: approach, files, how each criterion will be shown, check commands, risks |
| Plan gate | You, only if needed | Answers to open questions, approval of inferred criteria, acknowledgement of risks |
| Implement | Implementer | Local commits and `handoff.md` |
| Checks | Orchestrator | The plan's check commands, judged by exit codes only |
| Verify | Spec checker, five reviewers, custom verifiers, in parallel | One findings report each |
| Fix | Fresh implementer, at most twice | New commits and a response to every blocking finding |
| Close | Orchestrator | Final report |

The plan gate stops only when the plan has open questions, proposes acceptance criteria that the story did not state, or touches a risk area (auth, payments, data migration or deletion, public contracts, anything irreversible). Otherwise you get a one-line notice and the run continues.

## Outcomes

| Outcome | Meaning |
|---|---|
| `READY_FOR_REVIEW` | Checks pass, every criterion is met with evidence, and no Critical or Major finding is open. Review the branch and merge it yourself. |
| `NEEDS_HUMAN` | A decision is yours: a disputed finding, exhausted fix rounds, an ambiguity, or something no verifier could check. The report lists the options. |
| `BLOCKED` | Information, access, or a prerequisite is missing, or the task is larger than one story. |

A skipped check, a verifier that failed to run, or a criterion nobody could verify is never counted as a pass.

## Run artifacts

Everything is in `.orchestrator/<run-id>/` at the repository root. The directory ignores itself through `.orchestrator/.gitignore`, so it never shows in `git status` or in the diff under review. Delete it when you no longer need the record.

## Project configuration

You can add an optional section to the project's `AGENTS.md` or `CLAUDE.md`. Every line is optional, and the prompt overrides it for one run.

```markdown
## Story orchestrator
- Extra verifiers: ui-verifier
- Skip lenses: performance
- Plan approval: always
- Fix round cap: 2
- Branch naming: feat/<ticket>-<slug>
- Models: planner=fable, implementer=sonnet
```

The lenses are `correctness`, `readability`, `architecture`, `security`, and `performance`. All roles read the project's own instruction files for conventions and commands, so those files should name the exact test, lint, and build commands.

## Writing a custom verifier

A concern the built-in lenses do not cover, such as accessibility or a UI check, becomes a custom verifier. It is a project agent that the orchestrator starts in the verify stage alongside the built-in verifiers.

```markdown
---
name: ui-verifier
description: Starts the app and checks the screens a story changes. Started by the implement-story orchestrator.
tools: Read, Grep, Glob, Bash, Write
skills: verifier-contract
---

Your question: <what this verifier checks>. Finding prefix: `UI`.
<method>
```

Preloading `verifier-contract` gives it the shared rules: what it receives, that it writes only its own report and leaves tracked files untouched, the report format, the severity scale with its evidence rules, delta mode, and the verdict envelope. Put it in `.claude/agents/` and list it under "Extra verifiers".

## Permissions

Plugin agents cannot set their own permission mode, so your session's permission rules apply to every role. For an attended run with fewer prompts, allow the project's check commands and plain `git` read commands. The implementer also needs file edits and `git add` and `git commit`.

Roles must start with a fresh context. If your settings let the main session fork the conversation into sub-agents, deny forks for these runs:

```json
{ "permissions": { "deny": ["Agent(fork)"] } }
```

## What it will not do

It does not push, open pull requests, merge, rebase, reset, or delete branches. It does not split epics or large specifications into stories; use the `break-down` plugin for that. It does not run unattended or in batches, and it never runs more than one implementer at a time.

## Porting to another host

Role bodies are written against capabilities, not tools. Only the frontmatter is specific to Claude Code.

| Agent | Claude Code frontmatter | Capability it stands for |
|---|---|---|
| `orchestrator` | inherits all tools except `Edit`, `Write`, `NotebookEdit` | Sub-agents, shell, asking the user, and whatever tracker access the session has. It writes run files through the shell. |
| `planner` | `Read, Grep, Glob, Bash, Write` | Read, search, shell, and writing its one file. It cannot start agents. |
| `implementer` | inherits all tools except `Agent` | Everything except starting agents |
| `reviewer`, `spec-checker` | `Read, Grep, Glob, Bash, Write`, preloaded `verifier-contract` | Read, search, shell, and writing its report. It cannot start agents. |

For another host, map the same capabilities, check that a sub-agent starts with a fresh context rather than a copy of the parent conversation, and check that the contract skill can be preloaded. If it cannot be preloaded, inline the skill into both verifier definitions.
