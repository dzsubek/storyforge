# storyforge

A Claude Code marketplace for turning product specs into implementable work, then shipping that work with structured planning, test-first implementation, and independent verification.

Storyforge is for teams that want Claude Code to follow a repeatable path from requirements to a reviewed change: decompose a document into epics and stories, then run one story through plan → implement → check → blind review without inventing scope.

## Install

Add the marketplace from GitHub:

```bash
claude plugin marketplace add dzsubek/storyforge
```

From a local checkout:

```bash
claude plugin marketplace add ./
```

Install a plugin by name and marketplace:

```bash
claude plugin install <plugin>@storyforge
```

## Plugins

### break-down

Break a specification into epics, then into implementation-ready stories, without dropping or inventing requirements.

```bash
claude plugin install break-down@storyforge
```

| Command | What it does |
|---|---|
| `/break-down:epic-creator` | Decompose a spec, PRD, or requirements document into epics with source traceability. A document that is too small to decompose becomes a structured brief. |
| `/break-down:story-creator` | Split one epic, feature, or brief into user stories with acceptance criteria, scope boundaries, and dependencies. |

Run epic-creator first when the input is a whole specification. Run story-creator when you already have one epic or a brief. Where results are written follows the project's `AGENTS.md` or `CLAUDE.md`. The skills do not assume a tracker.

### implement-story

Take one story-sized task from description to a reviewed, verified local branch. A coordinator plans the work, has one implementer build it test-first, runs the project's checks itself, and has independent verifiers check the result blind. A spec checker goes criterion by criterion, and reviewers each cover one lens: correctness, readability, architecture, security, or performance.

```bash
claude plugin install implement-story@storyforge
```

```bash
claude --agent implement-story:orchestrator "<task description or story link>"
```

A run ends with local commits and one outcome: `READY_FOR_REVIEW`, `NEEDS_HUMAN`, or `BLOCKED`. Project settings, custom verifiers, and permissions are covered in the [plugin README](plugins/implement-story/README.md).

## Typical flow

1. Install both plugins from this marketplace.
2. Point epic-creator at a spec to get epics (or a brief).
3. Point story-creator at one epic or brief to get stories with acceptance criteria.
4. Run implement-story's orchestrator on one story until you get a local branch ready for human review.

## Layout

```text
.claude-plugin/marketplace.json
plugins/<plugin>/
LICENSE
```

Each plugin lives under `plugins/`.

## License

MIT. See [LICENSE](LICENSE).
