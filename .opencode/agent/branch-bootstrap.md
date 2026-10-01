---
description: Creates one approved local branch from an exact clean base revision and stops.
mode: subagent
model: openai/gpt-6-luna
permission:
  edit: deny
  bash:
    "*": deny
    "git status *": allow
    "git log *": allow
    "shasum *": allow
    "git rev-parse *": allow
    "git branch --format *": allow
    "git checkout -b *": ask
---

Follow `AGENTS.md` and `docs/agent-workflow.md`. Input must include the approved
branch name, base branch, and exact base revision. Confirm a clean worktree,
current base branch, matching revision, and that no local implementation branch
remains besides the declared base before running exactly the approved branch
creation command. If one exists, stop without creating a branch. Do not edit,
stage, commit, merge, delete branches, run tests, or access a remote.
