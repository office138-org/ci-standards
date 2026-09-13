# CI Standards Architecture

## Responsibility split

PR execution is optimized for fast feedback. Post-merge execution owns comprehensive validation.

### PR path

`caller -> pr-gate.yml -> repository-safety.yml + lint/typecheck + governance.yml`

The static-quality and governance paths execute in parallel after repository safety passes. Static quality installs only Ruff and mypy; governance installs project dependencies once and runs governance plus critical tests.

### Post-merge path

`caller -> post-merge.yml -> PR provenance -> dependency setup -> pytest-xdist comprehensive suite`

## Repository boundaries

`ci-standards` owns orchestration and shared safety behavior. Product repositories own:

- runtime code
- dependency declarations
- product-specific governance tests
- product-specific critical-test selection
- xdist compatibility exceptions, if any

## Central version contract

`v1` is the compatible major standard line. Changes within `v1` must preserve caller input compatibility unless a coordinated migration is approved.

## Free-plan model

Central repositories are public and should use GitHub Free public-repository branch protection. Private product repositories retain operational Human Approval plus post-merge provenance detection as compensating controls where paid private-repository enforcement is unavailable.
