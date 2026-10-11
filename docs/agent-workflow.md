# Agent Workflow

This repository uses one workflow across agent tools. Tools do not share session
state; they share this process, `AGENTS.md`, and immutable plan artifacts.

## Roles

| Role | Lifetime | Authority |
| --- | --- | --- |
| `coordinator` | One interactive session | Dispatches and verifies handoffs; never plans, edits, or changes Git state. |
| `bootstrapper` | One governance draft, freeze, or commit operation | Creates the initial project-specific governance baseline only. |
| `planner` | One draft, amendment, or freeze operation | Inspects and writes only local plan artifacts. |
| `branch-bootstrap` | One approved branch | Creates an approved branch from the declared base; never edits or commits. |
| `implementer` | One approved task | Implements or corrects one approved plan and verifies it. |
| `reviewer` | One committed revision | Independently reviews the exact revision; never writes. |
| `integrator` | One accepted revision | Fast-forwards an accepted branch into local main; never edits. |

## Governance Bootstrap

When `docs/engineering-contract.md` is absent, normal delivery is unavailable.
The coordinator asks the questionnaire in `docs/governance-bootstrap.md`, then
dispatches a fresh bootstrapper with the recorded answers.

The bootstrapper drafts only the project-specific documents named in that guide.
The coordinator presents a SHA-256 for every proposed file. The user approves the
exact set of hashes before a fresh bootstrapper freezes or commits anything.

After explicit approval, a bootstrapper commits only the approved governance
baseline locally. It must not create a branch, alter remotes, or make unrelated
changes. If the repository has no Git history, it may create the initial local
commit. The user performs all Git remote operations.

## Plan Artifacts

Planning state is local and ignored under `.ai/plans/`.

- `.ai/plans/draft.md` is mutable. A fresh planner may overwrite it after a current
  repository inspection.
- `.ai/plans/approved/<branch-slug>-<base-short-sha>-<plan-short-sha>.md` is the
  immutable, byte-identical snapshot approved by the user.

- `.ai/plans/approved/<original-name>-a<n>.md` is an immutable, approved
  amendment to that snapshot (see Scope, completion work, and amendments).

The coordinator hashes a draft before presenting it. Approval applies only to that
SHA-256. The planner freezes the draft only after verifying that hash. A mutable
draft, pasted conversation text, or summary is never an implementation handoff.

Every complete plan states the outcome, the acceptance criteria that prove it
(behavior and tests), the boundaries that must not change, the exact base
revision, branch and creation command, ordered changes, expected files, gate,
risks, and non-goals. Approval binds the outcome and boundaries; the expected
file list guides review and is not an allowlist.

## Delivery

1. A fresh planner inspects the repository and writes one complete draft.
2. The coordinator presents its SHA-256. The user approves or requests a new draft.
3. A fresh planner freezes the verified draft under `.ai/plans/approved/`.
4. A fresh branch-bootstrap agent creates the approved branch from the exact base.
5. A fresh implementer verifies the snapshot, branch, and base before editing,
   then runs the project-specific gate and commits.
6. A fresh reviewer verifies the same snapshot and reviews the exact commit.
7. Blocking in-scope findings, including missing completion work, go to an
   implementer in correction mode with the same artifact. A small, bounded
   material gap goes through an amendment. Only a change to the outcome,
   behavior, or architecture itself requires a new complete draft.
8. A fresh integrator verifies the accepted commit, fast-forwards local `main`,
   and deletes the branch.
9. Agents stop. The user owns Git remote operations.

## Scope, completion work, and amendments

Approval binds an outcome, not a file list. A change is material only when it
alters approved behavior, architecture, a public API or persisted format, a
contract rule, governance, or product policy, or adds a dependency or external
service. Nothing else is material.

Completion work is in scope by default: whatever the approved outcome needs to
pass the gate and satisfy the contract, including tests for changed code,
fixtures, documentation lines, build or lint repairs, and mechanical
registration of plan-named files in gate tables or exception registries. The
implementer does it without asking and lists each item under "Incidental
changes" in its handoff. The reviewer confirms each item is necessary and not
material. Missing completion work is a blocking in-scope finding, never a new
plan.

A material gap that is small and bounded takes an amendment, not a new draft.
A fresh planner writes a delta-only amendment to `.ai/plans/draft.md`: what
changes, why, and which acceptance criteria or boundaries change, one page at
most. The coordinator presents its SHA-256, the user approves, and a fresh
planner freezes it as `.ai/plans/approved/<original-name>-a<n>.md`. Work
continues on the same branch in continuation mode under the original artifact
plus its amendments. No repository re-inspection, restatement of the original,
or new branch. A full new draft is required only when the outcome, behavior, or
architecture itself changes.

A planner never writes a non-goal that contradicts a contract requirement, such
as excluding tests the contract requires for changed code or omitting gate
registration for a new file. Such a non-goal is a planning defect; completion
work overrides it and the reviewer reports it.

## Git remote restrictions

Git remote operations remain user-owned and outside agent authority. Agents
must not fetch, pull, push, change Git remotes, or perform other Git remote
operations. Read-only web and source research is permitted when it is within
the dispatched task and role and allowed by effective instructions and existing
permissions. This grants no external write, publishing, or messaging authority
and does not bypass sandbox or network restrictions. Research permission does
not expand a role's file-write or Git authority.

## Boundaries

Work starts only from a user-approved plan after governance bootstrap. One
implementation branch may be in flight. Before creating a branch, the
coordinator and branch-bootstrap agent must verify that no local implementation
branch remains besides the declared base; an existing branch must be reviewed,
integrated, and deleted before another is created. Branch creation, staging,
and commits are authorized by the user's approval of the exact plan hash;
fast-forward integration and branch deletion are authorized by the user's single
approval of the exact accepted commit. Tracked tool permission rules that let
those commands run without a native prompt do not waive either approval.

Technical decisions belong to the fresh planner. The coordinator asks the user
only for unresolved product or architecture policy, approval of exact plan and
amendment hashes, and approval of the exact commit to integrate. Completion work
and in-scope corrections are never sent to the user as decisions.
