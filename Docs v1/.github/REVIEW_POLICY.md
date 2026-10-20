# Hosting configuration still required

This directory configures an offline CI workflow. Nothing has been published,
and repository settings are not accessible or modified by this milestone.

When this app becomes a repository root, the owner must identify actual reviewers,
create CODEOWNERS entries for `/src/Contracts/`, `/contracts/`, `/architecture/`,
`/scripts/`, `/tests/`, `/.github/` and security implementations, then require their
reviews and the two PHP matrix checks in a branch ruleset. Require current approvals
after new commits, disallow bypasses as appropriate, and protect policy ownership.

No CODEOWNERS with a guessed username is supplied. CI files and a review template
alone do not block merging. A failing local check blocks only that local command.
