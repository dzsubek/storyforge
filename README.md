# dzsubek-hu-agent-mp

A Claude Code marketplace for custom plugins.

## Install

Add the marketplace from GitHub:

```bash
claude plugin marketplace add dzsubek-hu/agent-mp
```

From a local checkout, use the directory instead:

```bash
claude plugin marketplace add ./
```

Install a plugin by its name and this marketplace:

```bash
claude plugin install <plugin>@dzsubek-hu-agent-mp
```

## Plugins

### break-down

Break a specification into epics, then into implementation-ready stories, without dropping or inventing requirements.

```bash
claude plugin install break-down@dzsubek-hu-agent-mp
```

| Command | What it does |
|---|---|
| `/break-down:epic-creator` | Decompose a spec, PRD, or requirements document into epics with source traceability. A document that is too small to decompose becomes a structured brief. |
| `/break-down:story-creator` | Split one epic, feature, or brief into user stories with acceptance criteria, scope boundaries, and dependencies. |

Run epic-creator first when the input is a whole specification. Run story-creator when you already have one epic or a brief. Where the results are written follows the project's `AGENTS.md` or `CLAUDE.md`. The skills do not assume a tracker.

## Layout

```text
.claude-plugin/marketplace.json
plugins/<plugin>/
docs/
```

Each plugin lives in its own directory under `plugins/`. `docs/` holds research and notes. It is not part of an installed plugin.
