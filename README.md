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
- `.codex/`: Codex project instructions and native custom-agent definitions.
- `openrig/`: explicit optional OpenRig RigSpecs and reusable role AgentSpecs.
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
claude --agent coordinator
```

Then ask it to start the shared workflow, for example:

```text
Start the shared workflow for: <requested change>
```

`CLAUDE.md` directs Claude Code to `AGENTS.md`; the matching role adapters in
`.claude/agents/` use the same governance bootstrap and approved-plan workflow.

## Start With OpenRig

See the official [OpenRig getting-started guide](https://openrig.dev/docs/getting-started)
for installation and authentication before running any of the commands below.
Three prebuilt RigSpecs are included; each must be launched explicitly by
supplying the path to `rig up`.

### Model assignments by rig variant

| Role | Claude (`openrig/claude`) | Codex (`openrig/codex`) | Mixed (`openrig/mixed`) |
| --- | --- | --- | --- |
| coordinator | `claude-sonnet-4-6` | `openai/gpt-5.6-terra` | `openai/gpt-5.6-terra` |
| bootstrapper | `claude-sonnet-4-6` | `openai/gpt-6.1-sol` | `openai/gpt-6.1-sol` |
| planner | `claude-opus-4-8` | `openai/gpt-6.1-sol` | `claude-opus-4-8` |
| branch-bootstrap | `haiku` | `openai/gpt-5.6-luna` | `openai/gpt-5.6-luna` |
| implementer | `claude-sonnet-4-6` | `openai/gpt-5.6-terra` | `claude-sonnet-4-6` |
| reviewer | `claude-opus-4-8` | `openai/gpt-6.1-sol` | `openai/gpt-6.1-sol` |
| integrator | `haiku` | `openai/gpt-6-luna` | `openai/gpt-6-luna` |

The mixed variant uses the Claude model for `claude-code` seats (planner,
implementer) and the GPT model for `codex` seats (all other roles).

### Rig options

**All seats on Claude Code:**

```sh
rig up openrig/claude/rig.yaml
```

**All seats on Codex:**

```sh
rig up openrig/codex/rig.yaml
```

**Claude planner and implementer seats with Codex for the other roles:**

```sh
rig up openrig/mixed/rig.yaml
```

Once the coordinator seat is running, interact with it directly through the
OpenRig TUI or messaging to start the shared workflow.

The "Optional OpenRig placement" section below covers the structure and
constraints of these RigSpecs.

## Codex-native configuration without OpenRig

Trusted Codex projects can use `.codex/config.toml` for shared project
instructions and the standalone custom agents in `.codex/agents/*.toml`. The
root `AGENTS.md` remains the shared governance entry point. The definitions omit
model and permission settings: inherited defaults and account availability are
not equivalent to the existing standalone adapters. See the official
[Codex configuration guide](https://developers.openai.com/codex/config-file/config-basic),
[AGENTS.md guide](https://developers.openai.com/codex/agent-configuration/agents-md),
and [subagents guide](https://developers.openai.com/codex/agent-configuration/subagents.md)
for installation, authentication, trust, and native behavior. This repository
does not provide or claim a verified launch command.

## Optional OpenRig placement

OpenRig is optional and does not replace the standalone OpenCode, Claude Code,
or Codex-native placements. Supply one explicit RigSpec path to OpenRig:

- `openrig/codex/rig.yaml` for Codex seats;
- `openrig/claude/rig.yaml` for Claude Code seats; or
- `openrig/mixed/rig.yaml` for Claude planner/implementer seats and Codex for
  the other roles.

RigSpec-relative `agent_ref` and `cwd` paths resolve from the selected rig
directory. Each referenced AgentSpec is under `openrig/agents/<role>/agent.yaml`.
Its required `guidance/role.md` is delivered as `send_text` after harness
readiness, so no work may be assigned until the seat acknowledges its bounded
role and dispatch. Empty topology edges do not start, order, or enforce the
workflow. `relaunch_fresh` is only restore behavior, not proof of fresh per-task
workers or independent review.

OpenRig may write managed or other runtime configuration. Claude/mixed rigs use
`CLAUDE.local.md` rather than the tracked `CLAUDE.md`; Codex managed blocks can
still target `AGENTS.md`, and native/global trust or settings writes may occur.
Read the official [OpenRig getting-started guide](https://openrig.dev/docs/getting-started)
for installation and authentication. No OpenRig or harness execution is included
or validated here. These static files do not establish provider compatibility,
permission parity, orchestration, session isolation, artifact transport, or
review acceptance.

## Before Use

1. Initialize Git if the project is not already a repository.
2. Replace generic scaffold files only through the approved bootstrap flow.
3. Restart OpenCode after changing `opencode.json` or files under `.opencode/`.

The scaffold prohibits agents from remote Git operations. Pushes, pulls, fetches,
remote changes, and GitHub operations remain user-owned.
