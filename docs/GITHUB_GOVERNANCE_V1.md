# GitHub Governance v1

GITHUB_GOVERNANCE_VERSION = 1

## Scope

This standard governs GitHub lifecycle and CI execution for repositories that adopt `office138-org/ci-standards`.

## Mandatory lifecycle

1. Work on a non-main branch.
2. Direct push to `main` is prohibited.
3. A pull request is mandatory.
4. The PR Quality Gate must pass.
5. Human Approval is mandatory after the final material change.
6. Auto Merge remains disabled.
7. Merge is performed manually after Human Approval.
8. Post-Merge Full Validation runs on the default branch.
9. A post-merge failure blocks release and formal closure until corrected or reverted.

## PR Quality Gate

Mandatory pre-merge checks:

- repository safety
- Ruff correctness lint on changed Python
- mypy on changed production Python
- repository/product governance validation
- critical smoke tests
- important unit tests

Target: <= 3 minutes. Preferred target: 1-2 minutes.

The PR gate must not duplicate the comprehensive post-merge suite.

### Typecheck policy

`STRICT_CHANGED_FILES` is the default policy. Every changed production Python file must satisfy the established mypy invocation with zero errors. A repository may explicitly select the bounded `NO_NEW_ERRORS` compatibility policy only when legacy pre-existing mypy debt is demonstrated and Human Approval is recorded. `NO_NEW_ERRORS` runs the same mypy invocation and version against BASE and HEAD, permits only baseline-existing error multiplicity, and rejects every new error identity or increased count. It is opt-in, does not disable type checking, and does not alter other repositories' default behavior. Materialization, parsing, comparison, or fatal mypy execution failure fails closed.

## Post-Merge Full Validation

The comprehensive repository test tree must cover full regression, integration, E2E, and exhaustive validation. Python repositories use `pytest -n auto --dist worksteal` by default after bounded compatibility validation.

## GitHub Free private-repository compensating control

Where GitHub Free cannot technically enforce PR-only changes on a private repository, the standard post-merge workflow validates that a default-branch push SHA is associated with a merged PR targeting the default branch. Failure is reported as `GOVERNANCE_VIOLATION_DIRECT_MAIN_PUSH`.

This control detects rather than reverses a violating push; downstream release/formal-closure activity remains blocked until the repository returns to a compliant green state.

## Security baseline

- default workflow permissions are read-only
- `pull-requests: read` is added only where PR provenance is required
- third-party actions are pinned to full commit SHA
- checkout credentials are not persisted
- reusable workflows do not inherit secrets by default
- forbidden sensitive paths are rejected by repository safety
- obsolete PR runs should be cancelled by caller concurrency

## Exceptions

Exceptions must be repository-specific, documented, bounded, and Human-approved. They must not silently remove equivalent validation coverage.
