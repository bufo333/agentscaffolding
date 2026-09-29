# Project Agent Guide

Read `docs/agent-workflow.md` before changing source, tests, build tooling, CI,
agent configuration, or project governance.

If `docs/engineering-contract.md` does not exist, this project has not completed
governance bootstrap. The coordinator must follow `docs/governance-bootstrap.md`
before any implementation work begins.

## Operating Rules

- Read a target file fresh before analyzing or changing it.
- Do not invent names, values, URLs, external behavior, source claims, or project
  constraints. Verify them from a current source read or identify them as
  unverified.
- Do not bypass an accepted contract, test, architectural boundary, security rule,
  or quality gate to complete work.
- The user owns all remote operations. Never push, fetch, pull, change remotes, or
  use GitHub on the user's behalf.
- Keep changes within an approved plan. Stop and report a conflict between the
  plan, current repository state, and an accepted project contract.

## Project-Specific Rules

`docs/engineering-contract.md` defines the accepted language, tooling,
architecture, security, quality, and delivery constraints for this project.
`docs/project-architecture.md`, when present, is its architectural authority.

## Workflow

The approved plan artifact is the only implementation handoff. Follow
`docs/agent-workflow.md`. A project that has not accepted its initial governance
baseline may perform only the bootstrap workflow in `docs/governance-bootstrap.md`.
