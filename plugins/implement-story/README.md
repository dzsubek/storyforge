# Implement Story

Take one story-sized task from description to a reviewed, verified change on a local branch. A coordinator runs a fixed procedure. A planner writes a short plan in slices, a fresh implementer builds each slice test-first, the coordinator runs the project's checks itself, and then independent verifiers check the result blind: a spec checker goes criterion by criterion, and four lens reviewers each look for one kind of defect. Serious findings go back to a fresh implementer for at most two fix rounds, and the verifiers whose findings were answered check the fixes.

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
- **Extra verifiers**, extra or skipped lenses, or a different fix-round cap for this run.
- **Polish round.** What happens to Minor and Nit findings once nothing blocks: `ask` (the default), `auto`, or `never`.
- **Models per role**, such as "Use Sonnet for the implementer and Fable for the planner." Roles you do not mention keep the model in their definition (`inherit`, so the session's model). Also settable as `- Models: planner=fable, implementer=sonnet` in the project configuration below.

To resume an interrupted run, start the orchestrator again with no task, or with "resume".

## What happens

| Step | Who | Result |
|---|---|---|
| Intake | Orchestrator | `task.md`: the task word for word, with numbered acceptance criteria. If the story cites sources the session cannot reach, you are asked before planning starts |
| Plan | Planner | `plan.md`: approach, files, how each criterion will be shown, the cited sources it read, check commands and the environment they need, risks |
| Plan gate | You, only if needed | Answers to open questions, approval of inferred criteria, acknowledgement of risks |
| Implement | One fresh implementer per plan slice, in order | Local commits and `handoff.md` |
| Checks | Orchestrator | The services and build outputs the plan lists are started, then the plan's check commands run, judged by exit codes only |
| Verify | Spec checker, four reviewers, custom verifiers, in parallel | One findings report each. They use the environment the orchestrator already has running |
| Fix | Fresh implementer, at most twice | New commits, a response to every blocking finding, and the cheap non-blocking ones fixed in the same pass |
| Re-verify | Spec checker, correctness reviewer, and every verifier whose finding was answered | Each fix read as new code |
| Gaps | You, once, after the fixes | A waiver or a stop for each thing no verifier could check |
| Polish | Fresh implementer, at most once | The Minor and Nit findings still open, by the `Polish round` setting |
| Close | Orchestrator | Final report |

The plan gate stops only when the plan has open questions, proposes acceptance criteria that the story did not state, or touches a risk area (auth, payments, data migration or deletion, public contracts, anything irreversible). Otherwise you get a one-line notice and the run continues. A criterion whose target another story will build (a screen or service that does not exist yet) is always an open question: you decide what the control does in the meantime.

A source that says something different from the criterion citing it is an open question too, as is a cited source the planner could not open. The contradiction costs one question here; found after the code is written, it costs a round.

If one of your answers after the plan gate changes what the candidate must do, such as a ruling between two readings of a criterion, the orchestrator runs a **change round**. It does not count against the fix-round cap, and every verifier reads its result.

With `Polish round: ask` you are asked once, before the close, which of the open Minor and Nit findings to fix; with `auto` every open Minor is fixed without a question; with `never` they are only listed. The polish round does not count against the cap. After a report you can still ask for named non-blocking findings to be fixed ("fix COR-3 and ARCH-1"). That runs as a normal fix round and counts against the cap.

## Outcomes

| Outcome | Meaning |
|---|---|
| `READY_FOR_REVIEW` | Checks pass, every criterion is met with evidence, and no Critical or Major finding is open. Review the branch and merge it yourself. |
| `READY_WITH_EXCEPTIONS` | As above, except for gaps you waived: a criterion or check that could not be verified here, such as a target screen that does not exist yet or a device nobody could run. The report lists the exceptions first. |
| `NEEDS_HUMAN` | A decision is yours: a disputed finding, exhausted fix rounds, an ambiguity, or an unverified item you did not waive. The report lists the options. |
| `BLOCKED` | Information, access, or a prerequisite is missing, or the task is larger than one story. |

A skipped check, a verifier that failed to run, or a criterion nobody could verify is never counted as a pass. Only you can waive one, and you are asked once, after the fix rounds, not before them.

## Run artifacts

Everything is in `.orchestrator/<run-id>/` at the repository root. The directory ignores itself through `.orchestrator/.gitignore`, so it never shows in `git status` or in the diff under review. Delete it when you no longer need the record.

## Project configuration

You can add an optional section to the project's `AGENTS.md` or `CLAUDE.md`. Every line is optional, and the prompt overrides it for one run.

```markdown
## Story orchestrator
- Extra verifiers: ui-verifier
- Extra lenses: performance
- Skip lenses: readability
- Plan approval: always
- Fix round cap: 2
- Polish round: auto
- Branch naming: feat/<ticket>-<slug>
- Models: planner=fable, implementer=sonnet
```

The default lenses are `correctness`, `readability`, `architecture`, and `security`. `performance` is off by default, because most stories have no performance surface and the correctness lens covers the performance defects that do occur; add it under "Extra lenses" for work where scale matters. All roles read the project's own instruction files for conventions and commands, so those files should name the exact test, lint, and build commands.

The planner and the verifiers can use the lookup tools your session provides, such as a documentation or tracker connector, to open the sources a story cites. Name the connector in the prompt or the project instructions when requirements live outside the repository. The orchestrator makes one call to it at intake and stops to ask you if it cannot be reached: you can fix the connection, name a local copy to read in its place, or accept a run without the sources.

Name the commands that start a test database or another service, and any build step a check depends on, in the project's instruction files as well. The planner copies them into the plan's "Verification environment", and the orchestrator starts them before the checks and keeps them available to the verifiers.

## Writing a custom verifier

A concern the built-in lenses do not cover, such as accessibility or a UI check, becomes a custom verifier. It is a project agent that the orchestrator starts in the verify stage alongside the built-in verifiers.

```markdown
---
name: ui-verifier
description: Starts the app and checks the screens a story changes. Started by the implement-story orchestrator.
disallowedTools: Edit, NotebookEdit, Agent, Artifact, ArtifactData, ArtifactComments, SendMessage, EnterWorktree, ExitWorktree
skills: verifier-contract
---

Your question: <what this verifier checks>. Finding prefix: `UI`.
<method>
```

Preloading `verifier-contract` gives it the shared rules: what it receives, that it writes only its own report and leaves tracked files untouched, the report format, the severity scale with its evidence rules, delta mode, how to pass an observation to another verifier, and the verdict envelope. A tool denylist, as above, keeps the session's lookup and device tools available to it; an allowlist (`tools:`) would remove them. The names after `Agent` are tools no verifier uses. Their definitions are sent with every step an agent takes, so denying them makes each step smaller. Put it in `.claude/agents/` and list it under "Extra verifiers".

## Permissions

Plugin agents cannot set their own permission mode, so your session's permission rules apply to every role. For an attended run with fewer prompts, allow the project's check commands and plain `git` read commands. The implementer also needs file edits and `git add` and `git commit`.

Roles must start with a fresh context. If your settings let the main session fork the conversation into sub-agents, deny forks for these runs:

```json
{ "permissions": { "deny": ["Agent(fork)"] } }
```

## What it will not do

It does not push, open pull requests, merge, rebase, reset, or delete branches. It does not split epics or large specifications into stories; use the `break-down` plugin for that. It does not run unattended or in batches, and it never runs more than one implementer at a time: the slices of a plan are built one after another.

## Porting to another host

Role bodies are written against capabilities, not tools. Only the frontmatter is specific to Claude Code.

| Agent | Claude Code frontmatter | Capability it stands for |
|---|---|---|
| `orchestrator` | inherits all tools except `Edit`, `Write`, `NotebookEdit`, and the unused tools below | Sub-agents, shell, asking the user, and whatever tracker access the session has. It writes run files through the shell. |
| `planner` | inherits all tools except `Edit`, `NotebookEdit`, `Agent`, and the unused tools below | Read, search, shell, the session's lookup tools, and writing its one file. It cannot start agents. |
| `implementer` | inherits all tools except `Agent` and the unused tools below | Everything except starting agents |
| `reviewer`, `spec-checker` | inherits all tools except `Edit`, `NotebookEdit`, `Agent`, and the unused tools below; preloaded `verifier-contract` | Read, search, shell, the session's lookup tools, and writing its report. It cannot start agents. |

Every role also denies tools it never uses: `Artifact`, `ArtifactData`, `ArtifactComments`, `SendMessage`, `EnterWorktree`, and `ExitWorktree`. The orchestrator additionally denies `DesignSync`, `RemoteTrigger`, `CronCreate`, `CronDelete`, `CronList`, `ScheduleWakeup`, `EnterPlanMode`, and `ExitPlanMode`. These stand for no capability. They are denied because a tool's definition is sent with every step, and these were about a quarter of each agent's starting context.

For another host, map the same capabilities, check that a sub-agent starts with a fresh context rather than a copy of the parent conversation, and check that the contract skill can be preloaded. If it cannot be preloaded, inline the skill into both verifier definitions.
