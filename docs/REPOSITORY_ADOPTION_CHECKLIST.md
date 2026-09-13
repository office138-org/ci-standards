# Repository Adoption Checklist

Use this checklist when connecting a repository to GitHub Governance v1.

- [ ] Repository uses a non-main development branch and PR workflow.
- [ ] PR caller references `office138-org/ci-standards/.github/workflows/pr-gate.yml@v1`.
- [ ] Post-merge caller references `office138-org/ci-standards/.github/workflows/post-merge.yml@v1`.
- [ ] Governance test paths are explicitly declared.
- [ ] Critical smoke and important unit test paths are explicitly declared.
- [ ] `pytest-xdist` is present and compatibility has been validated before enabling parallel post-merge execution.
- [ ] Auto Merge is disabled.
- [ ] Human Approval is required operationally after the final material PR change.
- [ ] Direct `main` push is prohibited.
- [ ] A post-merge failure blocks release/formal closure.
- [ ] Rollback path to the previous local workflow is documented for existing repositories.
