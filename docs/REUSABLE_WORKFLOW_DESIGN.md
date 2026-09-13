# Reusable Workflow Design

## PR caller contract

A product PR caller invokes `.github/workflows/pr-gate.yml@v1` and supplies:

- `base_sha`
- `head_sha`
- `python_version`
- `requirements_file`
- `cache_dependency_paths`
- `typecheck_roots`
- `typecheck_policy` (optional)
- `governance_tests`
- `critical_tests`

The caller should set `contents: read` and PR concurrency with `cancel-in-progress: true`.

Example caller:

```yaml
name: PR Quality Gate
on:
  pull_request:
    branches: [main]
permissions:
  contents: read
concurrency:
  group: ${{ github.workflow }}-${{ github.event.pull_request.number }}
  cancel-in-progress: true
jobs:
  quality:
    uses: office138-org/ci-standards/.github/workflows/pr-gate.yml@v1
    with:
      base_sha: ${{ github.event.pull_request.base.sha }}
      head_sha: ${{ github.event.pull_request.head.sha }}
      python_version: "3.12"
      requirements_file: requirements-dev.txt
      typecheck_roots: "tools"
      typecheck_policy: "NO_NEW_ERRORS" # documented, Human-approved compatibility policy only
      governance_tests: "tests/test_governance.py"
      critical_tests: "tests/test_smoke.py tests/test_important_unit.py"
```

## Post-merge caller contract

A product caller invokes `.github/workflows/post-merge.yml@v1` on `push` to `main` and optionally `workflow_dispatch`.

It should grant `contents: read` and `pull-requests: read`. The default `require_pr_provenance: true` detects direct default-branch pushes on GitHub Free private repositories.

Example caller:

```yaml
name: Post-Merge Full Validation
on:
  push:
    branches: [main]
  workflow_dispatch:
permissions:
  contents: read
  pull-requests: read
jobs:
  full:
    uses: office138-org/ci-standards/.github/workflows/post-merge.yml@v1
    with:
      python_version: "3.12"
      requirements_file: requirements-dev.txt
      pytest_root: tests/
      xdist_workers: auto
      xdist_mode: worksteal
```

## Compatibility rule

`pytest-xdist` is the default post-merge execution model, but each existing repository must pass one bounded compatibility run before migration. A demonstrated incompatibility may use a documented Human-approved exception while retaining comprehensive coverage.

## Mypy compatibility policy

The optional `typecheck_policy` input accepts only `STRICT_CHANGED_FILES` and `NO_NEW_ERRORS`; its default is `STRICT_CHANGED_FILES`. A consuming repository must not pass `NO_NEW_ERRORS` until the referenced `@v1` declares that input. `NO_NEW_ERRORS` compares the base and head changed-production diagnostics as counted identities and fails on any new identity or excess count; it is not a typecheck bypass.
