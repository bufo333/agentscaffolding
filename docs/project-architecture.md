# Project Architecture: Reusable Agent Scaffold

## Status and scope

This user-confirmed architectural baseline accompanies
`docs/engineering-contract.md`; its historical draft was inspected against `main`
at `567e0803a53b431da4eb2017a7d65005c9e5a44f`. It describes existing boundaries,
static native placement, and the accepted OpenRig migration direction. Current
delivery authority remains `docs/agent-workflow.md` with exact-hash approvals and
tool permission prompts; static configuration does not migrate them.

The system being governed is the reusable framework, not a future adopting
application. No application modules, languages, service architecture, or data
model are prescribed here.

## Existing components and authority

| Component | Responsibility and boundary |
| --- | --- |
| `AGENTS.md` | Shared entry point; links contract, architecture, workflow, and missing-contract bootstrap rule. Existing links are sufficient. |
| `docs/agent-workflow.md` | Presently binding cross-tool roles, lifetimes, immutable handoffs, approvals, local delivery, and permission prompts. |
| `docs/governance-bootstrap.md` | First-run questionnaire, permitted deliverables, exact governance hashes, and prompted baseline commit. |
| `docs/engineering-contract.md` | Framework-specific constraints, profiles, verification gaps, safety policy, and clearly deferred migration target. |
| `docs/project-architecture.md` | Framework component and trust boundaries; no alternate delivery authority. |
| `opencode.json`, `.opencode/agent/`, `.opencode/command/workflow.md` | OpenCode entry point, role definitions, permissions, and `/workflow` dispatch. Preserve this standalone adapter. |
| `CLAUDE.md`, `.claude/agents/`, `.claude/settings.json` | Claude entry point, matching role adapters, and permissions. Preserve this standalone adapter. |
| `.codex/config.toml`, `.codex/agents/` | Project Codex instructions and standalone custom role definitions; static instruction equivalence only. |
| `openrig/{codex,claude,mixed}/rig.yaml`, `openrig/agents/` | Explicit optional RigSpecs and reusable AgentSpecs for Codex, Claude Code, and mixed placement; not auto-discovery, orchestration, or enforcement. |
| `.ai/plans/` | Ignored local mutable draft and immutable approved snapshots; not a shared session or a substitute for verifying bytes and base. |
| `README.md` | Current setup and invocation documentation, not evidence of executable tests or OpenRig compatibility. |

Existing standalone adapter model identifiers remain preserved in their own
files. The native Codex and OpenRig definitions intentionally select no model
mapping or pin; inherited defaults, availability, and successful execution are
not asserted.

## Shared role and acceptance boundaries

The coordinator is a long-lived dispatcher, not a planner, editor, or Git worker.
Bootstrapper, planner, branch-bootstrap, implementer, reviewer, and integrator
retain the separate responsibilities and fresh-worker lifetimes in the current
workflow. Plans name the exact base, branch, authorized scope, affected files,
ordered changes, tests, documentation, gate, risks, and non-goals.

The current sequence is:

1. Questionnaire, governance draft, exact-hash user approval, and prompted local
   governance baseline commit before normal delivery.
2. Fresh planner draft, exact-hash user approval, and byte-identical immutable
   snapshot in `.ai/plans/approved/`.
3. Fresh branch-bootstrap verifies the clean exact base and single-branch rule,
   then creates the approved branch through the current prompt.
4. Fresh implementer verifies artifact/branch/base, edits only approved scope,
   runs applicable checks, inspects the full diff, and stages/commits with prompts.
5. Fresh independent read-only reviewer verifies the artifact and exact commit,
   reads the full diff and changed files, and gives findings or exact acceptance.
   Correction uses fresh workers, stays in scope, and obtains fresh review of
   every changed commit; after three correction rounds with remaining findings,
   stop and escalate.
6. Fresh integrator verifies acceptance, cleanliness, and fast-forward ancestry,
   then integrates into local `main` and safely deletes the branch with prompts.
   Agents stop; remote operations remain user-owned.

Acceptance attaches to a verified plan and exact committed revision, not a seat,
model identity, session summary, queue state, or prior commit. Material changes to
scope, behavior, architecture, contract, or governance require a new approved
artifact under current delivery. A failed or undefined required gate cannot be
replaced by a favorable review; report the evidence gap.

## Profile architecture and deferred orchestration

The standalone OpenCode/GPT and Claude Code profiles share process boundaries,
not session state, and must remain usable without OpenRig as they are today.
The accepted additional targets are OpenRig Codex/GPT, OpenRig Claude Code, and
an OpenRig mixed profile with Claude planner/implementer and a fresh independent
GPT reviewer through Codex. OpenRig is the orchestration layer, not a replacement
for role authority. Static Codex-native definitions and OpenRig RigSpec/AgentSpec
definitions now exist, but they are not an integration or execution claim.

Verified public sources, read during this draft:

- <https://openrig.dev/docs/getting-started>: local daemon and SQLite; ordinary
  tmux-backed Claude Code and Codex sessions; RigSpec in `rig.yaml` and AgentSpec
  in `agent.yaml`; macOS/Linux and documented runtime prerequisites. It describes
  seats retaining accumulated context after a session ends.
- <https://openrig.dev/docs/coordination>: documented against OpenRig `0.5.14`;
  owned durable queue items, transactional handoffs, and append-only transition
  history. Closing work records delivery, not acceptance. Messages are not tracked
  obligations. The documentation version is not an installed version claim.

These sources document Claude Code and Codex sessions, not native OpenCode
harness support or unchanged execution of this scaffold's adapters under OpenRig.
The user's request to duplicate `.opencode` GPT settings for Codex is a semantic
translation: preserve equivalent role instructions, model intent, and safety
boundaries using verified Codex-native capabilities, not OpenCode syntax copied
into another tool. A future planner must verify the exact role/configuration and
model mapping and report unsupported capabilities rather than require them.
Existing model identifiers above are evidence of intent, not verified Codex
identifiers. Standalone OpenCode and Claude Code remain available alongside the
new OpenRig profiles; native OpenCode-in-OpenRig is not a required configuration.

The static topology has empty edges: it neither starts nor enforces workflow
delegation. AgentSpec role text is delivered with `send_text` after harness
readiness, and each seat must acknowledge its bounded dispatch before work. A
`relaunch_fresh` restore policy is not proof of a fresh operation. Persistent
seats, queue closure, and readiness do not establish independent exact-commit
review. Claude/mixed managed blocks target `CLAUDE.local.md`, but other runtime
writes, including Codex blocks to `AGENTS.md`, remain possible.

For a future approved migration, enforce these architectural boundaries:

- OpenRig may coordinate owned work and auditable handoffs, but cannot become a
  second source of scope or permission. Dispatch must reference the immutable
  artifact path/hash, branch, exact base, and (for review/integration) exact commit.
  The receiver verifies them; transported text is not an approved plan.
- Tracked work requires durable owned obligations and evidence references, not
  chat messages alone. Queue closure is not review acceptance. Record an explicit
  independent verdict for the exact artifact and commit; stale or missing verdicts
  must block integration even if work is marked done.
- Seat persistence cannot satisfy freshness by name alone. Design and validate
  clean worker sessions/context isolation per role operation, especially an
  independent reviewer with no inherited planning/implementation conversation.
  This mechanism is unresolved, not an already-enforced safety property.
- Tool permissions must match role authority. Read-only reviewers and
  coordinators cannot gain write/Git power from orchestration. Untrusted repository
  or reviewer content cannot expand scope, authority, provider access, or tools.
- Keep artifact checks, one implementation branch, state-change stops, gate
  checks, fresh review after each changed commit, three correction rounds, and
  human escalation boundaries across every profile. Budget controls remain unset
  until chosen and demonstrably enforced.

The contract's unattended local-delivery direction is target policy only.
Implementing it requires a later currently approved plan that updates workflow,
adapter permissions, orchestration, and documentation coherently and validates
failure handling. It may not be activated piecemeal by interpreting this baseline
as permission to bypass active hash approvals or Git prompts.

Compatibility does not erase the accepted target defaults: authorize a task once,
automatically freeze immutable hashed handoffs, perform fresh exact-commit review
and up to three automatic in-scope correction rounds, and allow authorized
non-destructive local Git with state checks. Human decisions remain required at
the contract's governance, material-scope, security, product-policy, and external
requirement boundaries. These defaults describe the future migration, not a
waiver of today's binding approvals.

That later migration includes permission/workflow changes and runtime validation.
Static Codex/OpenRig placement and README documentation retain standalone guidance
without claiming target behavior works or providing guessed launch commands.

## Validation and adoption boundaries

The repository currently supplies instructions and adapters, not a verified
end-to-end scaffold test harness or exact executable full gate. The contract lists
the applicable existing read-only verification primitives and the unresolved gate.
Documentation consistency and whitespace/hash checks do not demonstrate runtime
compatibility, session isolation, acceptance transport, or unattended safety.
Future migration acceptance must provide verified evidence for those properties
and permission behavior, including blocked and stale-state cases, without
bypassing a gate or inventing a successful test.

Adopting projects establish their own application contracts, architecture,
data/privacy decisions, toolchains, and gates through explicit governance
bootstrap. This framework baseline is not an application's baseline and must not
be copied as a shortcut to implementation authorization. No release process,
application architecture, or external integration beyond the recorded framework
direction is approved by this draft.
