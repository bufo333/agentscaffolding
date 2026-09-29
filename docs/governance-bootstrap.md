# Governance Bootstrap

Use this guide only while `docs/engineering-contract.md` is absent. The
coordinator asks these questions before dispatching the bootstrapper. It may ask
follow-up questions when an answer leaves a project policy unresolved.

## Questionnaire

1. What problem does the project solve, who uses it, and what is explicitly out of scope?
2. Which languages, frameworks, package managers, and toolchain versions are required?
3. Which operating systems, runtimes, browsers, devices, or deployment targets are supported?
4. What commands format, lint, type-check, build, test, and run the full acceptance gate?
5. What architecture boundaries, module ownership rules, public APIs, and dependency rules must hold?
6. What correctness, performance, determinism, compatibility, accessibility, localization, or reliability requirements apply?
7. What data classifications, privacy requirements, secret-handling rules, and security constraints apply?
8. What external services, protocols, data sources, licenses, or dependency-admission rules constrain the project?
9. What test levels, coverage expectations, fixtures, manual checks, and CI requirements are required?
10. What documentation, changelog, migration, release, or versioning rules apply?
11. What Git branch, commit, review, integration, and local-versus-remote-operation rules apply beyond this scaffold?
12. Which decisions remain intentionally open, and who can resolve them?

## Bootstrap Deliverables

From verified answers and a fresh repository inspection, the bootstrapper drafts:

- `docs/project-architecture.md` when the project has meaningful architectural
  boundaries or the user requests one.
- `docs/engineering-contract.md`, defining accepted project-specific constraints
  and the exact quality gate.
- Updates to `AGENTS.md` only when needed to link project-specific authority.

The bootstrapper does not invent unanswered policies. It records unresolved items
as open questions and does not state them as requirements.

## Approval And Commit

The coordinator lists every proposed path and SHA-256. The user approves the exact
set of hashes. A fresh bootstrapper verifies the hashes, stages only those files,
and creates one local baseline commit through an explicit permission prompt.

Suggested commit subject: `Establish project governance baseline`.
