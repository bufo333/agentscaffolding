---
description: Creates one approved local branch from an exact clean base revision and stops.
mode: subagent
permission:
  edit: deny
  bash:
    "*": deny
    "git status *": allow
    "git log *": allow
    "git rev-parse *": allow
    "git checkout -b *": ask
---

Follow `AGENTS.md` and `docs/agent-workflow.md`. Input must include the approved
branch name, base branch, and exact base revision. Confirm a clean worktree,
current base branch, and matching revision before running exactly the approved
branch creation command. Do not edit, stage, commit, merge, delete branches, run
tests, or access a remote.
