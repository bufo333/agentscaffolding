# Agent Project Scaffold

A reusable, local-first agent workflow for new software projects. Copy this
scaffold into a repository before beginning work.

On a new project, the coordinator detects the missing engineering contract and
runs the governance questionnaire. After you approve the exact document hashes,
the bootstrapper creates the initial local governance commit. Normal planning and
implementation begin only after that baseline exists.

## Included

- `AGENTS.md`: shared project instructions.
- `docs/`: the portable workflow and first-run governance questionnaire.
- `.opencode/`: OpenCode roles and `/workflow` command.
- `.claude/`: matching Claude role adapters.
- `.ai/plans/`: ignored mutable and approved plan handoffs, created on demand.

## Start With OpenCode

From the project root, start an interactive OpenCode session:

```sh
opencode
```

The configured default agent is `coordinator`. Start work with:

```text
/workflow <requested change>
```

For a project without `docs/engineering-contract.md`, the coordinator asks the
governance-bootstrap questionnaire before allowing planning or implementation.
Answer the questions, review the generated document hashes, and explicitly
approve them before the bootstrapper makes the local baseline commit.

## Start With Claude Code

From the project root, start Claude Code:

```sh
claude
```

Then ask it to start the shared workflow, for example:

```text
Start the shared workflow for: <requested change>
```

`CLAUDE.md` directs Claude Code to `AGENTS.md`; the matching role adapters in
`.claude/agents/` use the same governance bootstrap and approved-plan workflow.

## Before Use

1. Initialize Git if the project is not already a repository.
2. Replace generic scaffold files only through the approved bootstrap flow.
3. Restart OpenCode after changing `opencode.json` or files under `.opencode/`.

The scaffold prohibits agents from remote Git operations. Pushes, pulls, fetches,
remote changes, and GitHub operations remain user-owned.
