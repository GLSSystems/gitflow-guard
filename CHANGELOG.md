# Changelog

## v1.0.0

- Reusable workflow `flow.yml`: PR origin check, `main` protection (accepts PRs from the integration branch or `hotfix/*`, in the repository or in a fork, merged by an account in `MAIN_APPROVERS`) and integration-branch sync after each accepted merge.
- Inputs `approvers`, `integration-branch`, `language` (`en`, `es`, for messages); output `main-accepted` to gate each project's deploy.
- `pre-push` hook, project workflow template and README sections (`en`, `es`).
- **Release** job in this repository's CI: after an accepted merge into `main`, tags and publishes the version at the top of this file.
- Documentation in English, with numbered sections and tables of contents.
