## <N>. Workflow

This repository uses [gitflow-guard](https://github.com/GLSSystems/gitflow-guard), version `vX.Y.Z` (pinned in `.github/workflows/ci.yml`):

- `main` is production and changes only through a PR merged by an account in `MAIN_APPROVERS`.
- Branches, roles, daily cycle, hotfix and rollback: [Workflow](https://github.com/GLSSystems/gitflow-guard/blob/main/docs/en/workflow.md).
- External contributors and automation accounts work from a fork: [Automation accounts](https://github.com/GLSSystems/gitflow-guard/blob/main/docs/en/automation-accounts.md).

After cloning:

```sh
git config core.hooksPath .githooks
```

Specific to this project:

| Topic | Detail |
|---|---|
| Validation | <what the `validate` job checks> |
| Deploy | <where and how the `deploy` job publishes> |
| Variables and secrets | `MAIN_APPROVERS` + <the project's own> |
| Post-deploy check | <URL or checks> |
