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
- `openrig/`: explicit optional OpenRig RigSpecs, reusable role AgentSpecs, and
  the Codex sandbox profile under `openrig/codex-profiles/`.
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

### One-time setup for Codex seats

Codex's default sandbox blocks all network access, including the local OpenRig
daemon, so a Codex seat cannot run `rig whoami` or `rig queue` without a manual
"run outside the sandbox" approval on every command. The `codex` and `mixed`
RigSpecs therefore set `codex_config_profile: scaffold-net` on every Codex
member. That named profile must exist in your Codex home before launch:

```sh
cp openrig/codex-profiles/scaffold-net.config.toml ~/.codex/scaffold-net.config.toml
```

If `CODEX_HOME` is set in the daemon's launch environment, copy it there instead.
The profile keeps the `workspace-write` sandbox and `on-request` approvals and
only enables `network_access`, which grants general network access to those
seats, not just the daemon. Without the file, `rig up` fails for Codex members.

The first time OpenRig launches Codex in a project, Codex shows a "Hooks need
review" dialog for OpenRig's activity hooks. Answer it in each Codex seat
terminal (`tmux attach -t <seat>`), then restart the rig: role guidance typed
while that dialog is open is lost, and seats that started without it fall back
to in-process subagents instead of queue dispatch. Codex records the trust in
`~/.codex/config.toml`, so later launches do not prompt.

### Model assignments by rig variant

| Role | Claude (`openrig/claude`) | Codex (`openrig/codex`) | Mixed (`openrig/mixed`) |
| --- | --- | --- | --- |
| coordinator | `claude-sonnet-4-6` | `gpt-5.6-terra` | `gpt-5.6-terra` |
| bootstrapper | `claude-sonnet-4-6` | `gpt-6.1-sol` | `gpt-6.1-sol` |
| planner | `claude-opus-4-8` | `gpt-6.1-sol` | `claude-opus-4-8` |
| branch-bootstrap | `haiku` | `gpt-5.6-luna` | `gpt-5.6-luna` |
| implementer | `claude-sonnet-4-6` | `gpt-5.6-terra` | `claude-sonnet-4-6` |
| reviewer | `claude-opus-4-8` | `gpt-6.1-sol` | `gpt-6.1-sol` |
| integrator | `haiku` | `gpt-6-luna` | `gpt-6-luna` |

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

### Using the rig

Seats are named `workflow-<role>@<rig-name>`, for example
`workflow-coordinator@scaffold-mixed`. Only the coordinator takes work from you.

Viewing all seven seats at once needs a terminal provider. OpenRig's default
is [herdr](https://herdr.dev), an agent multiplexer installed with Homebrew;
cmux is the alternative provider:

```sh
brew install herdr
rig terminal status        # confirms the provider is available and alive
```

Then open every seat as a tile in one window, or attach to a single seat
without a provider:

```sh
rig terminal open scaffold-mixed
tmux attach -t workflow-coordinator@scaffold-mixed
```

`rig tui` opens OpenRig's mission-control dashboard for monitoring; its
`terminal rig:scaffold-mixed` command opens the same tiles through the provider.

Describe the work in the coordinator's terminal. Each role's
`openrig/agents/<role>/guidance/role.md` tells the seat that its workers are the
other seats: the coordinator dispatches with `rig queue create` or
`rig queue handoff` addressed to a seat, and every worker claims its item, does
the bounded work, and hands the item back with `rig queue handoff`. In-process
subagents are prohibited for role work. Watch progress with:

```sh
rig ps --nodes --rig scaffold-mixed
rig queue list
rig transcript workflow-coordinator@scaffold-mixed --tail 50
```

A seat in `needs-input` is stuck on a native prompt that only you can answer.
Claude Code seats prompt for `rig` commands at first use; choosing the
"don't ask again" option for `rig whoami` and `rig queue` avoids repeats.

### Restarting

Relaunching immediately after `rig down` can leave a half-created rig whose
harness never started, and a second record of the same name blocks the next
`rig up` with an ambiguous-name error. The sequence that works reliably:

```sh
rig down scaffold-mixed --delete
rig ps --filter status=stopped   # confirm no record of that name remains
rig up openrig/mixed/rig.yaml
```

If `rig down --delete` reports sessions it cannot kill, run `tmux kill-server`,
restart the daemon with `rig daemon stop` and `rig daemon start` so it reconciles
the dead sessions, then delete the record and launch again.

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
role and dispatch. Each `role.md` carries the same role text as the native
adapters plus an "OpenRig seat protocol" section that maps the workflow's
"fresh worker" dispatch onto queue items between seats. Empty topology edges do
not start, order, or enforce the workflow. `relaunch_fresh` is only restore
behavior, not proof of fresh per-task workers or independent review.

Codex members in the `codex` and `mixed` RigSpecs carry
`codex_config_profile: scaffold-net`, a named Codex profile that OpenRig passes
as `-p scaffold-net` instead of its default `-s workspace-write`. The profile
source is `openrig/codex-profiles/scaffold-net.config.toml` and must be copied
into the Codex home directory; see "One-time setup for Codex seats" above.

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
