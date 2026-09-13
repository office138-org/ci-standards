# CI Standards

Central GitHub Actions standards for AI-assisted repositories governed by GitHub Governance v1.

## Purpose

This repository is the single source of truth for shared CI execution policy. Product repositories keep only thin caller workflows and product-specific test selections.

## Reusable workflows

- `.github/workflows/repository-safety.yml` — forbidden-path and diff safety gate.
- `.github/workflows/governance.yml` — repository/product governance plus critical smoke and important unit tests.
- `.github/workflows/pr-gate.yml` — PR orchestration: repository safety, Ruff correctness lint, changed-production mypy, governance, critical smoke, important unit.
- `.github/workflows/post-merge.yml` — default-branch PR provenance check and comprehensive pytest-xdist validation.

## Performance target

PR Quality Gate target: <= 3 minutes. Preferred target for Python AI repositories: 1-2 minutes.

The PR gate does not run the comprehensive suite. Full regression, integration, E2E, and exhaustive validation run only after merge or explicit manual validation.

## Versioning

Product repositories consume a controlled major reference such as `office138-org/ci-standards/.github/workflows/pr-gate.yml@v1`.

Changes to the `v1` line require Human Approval and compatibility validation. A product repository may pin an immutable commit SHA when stricter reproducibility is required.

## Human Approval

CI does not replace Human Approval. Auto Merge remains disabled under GitHub Governance v1.
