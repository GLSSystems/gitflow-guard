# gitflow-guard

A GitFlow workflow for GitHub projects where `main` is production and **changes only through a Pull Request merged by an owner account**. It works on GitHub's free plan, including private repositories.

It serves people and automation accounts (bots, CI tools, assistants) alike: everyone follows the same flow and everyone stops at the same checkpoint, the PR to `main`.

## Contents

- [What it includes](#what-it-includes)
- [Documentation](#documentation)
- [Adopting it in a project](#adopting-it-in-a-project)
- [Languages](#languages)
- [Versions](#versions)

## What it includes

| Path | What it is |
|---|---|
| `.github/workflows/flow.yml` | Reusable workflow: PR origin check, `main` protection and integration-branch sync |
| `.githooks/pre-push` | Local hook that blocks direct pushes to `main` |
| `templates/project-ci.yml` | A project's workflow that calls the shared flow, with validation and deploy |
| `templates/pre-push` | Copy of the hook to install in each project |
| `templates/readme-section.<lang>.md` | Section for each project's README |
| `.github/workflows/ci.yml` | CI of this repository (uses its own shared flow) |

## Documentation

| Document | For |
|---|---|
| [Workflow](docs/en/workflow.md) | Everyone: branches, roles, daily cycle, hotfix, rollback, rules |
| [How `main` is protected](docs/en/protection.md) | Everyone: protection layers and why automation accounts work from a fork |
| [Adopting the flow in a project](docs/en/adopting.md) | Owner: organization and repository setup |
| [Automation accounts](docs/en/automation-accounts.md) | Owner and automation account: fork, credentials and daily work |
| [Verification](docs/en/verification.md) | Owner: checks after setting up or changing anything |

## Adopting it in a project

Summary (details in [Adopting the flow in a project](docs/en/adopting.md)):

1. Copy `templates/project-ci.yml` to the project's `.github/workflows/ci.yml` and pin the version of `flow.yml`.
2. Choose the `language` of messages.
3. Fill in the project's `validate` and `deploy` jobs.
4. Create the `MAIN_APPROVERS` variable.
5. Copy `templates/pre-push` to `.githooks/pre-push`.
6. Add the section from `templates/readme-section.<lang>.md` to the project's README.

## Languages

The documentation of this repository is in English. Each project chooses its own language:

| Element | Available languages | How to choose |
|---|---|---|
| Messages of `flow.yml` (job names stay in English, so check names are the same in every project) | `en`, `es` | `language` input in the project's workflow |
| README section | `en`, `es` | `templates/readme-section.<lang>.md` |

To add a language: add its messages to each `if [ "$LANGUAGE" = ... ]` block in `flow.yml`, add `templates/readme-section.<lang>.md`, and update this table. Translated documentation goes in `docs/<lang>/` with the same file names.

## Versions

- Each version is a `vX.Y.Z` tag with an entry in [CHANGELOG.md](CHANGELOG.md).
- Projects pin the **commit** of a version, not a branch. A change here affects no project until its owner updates that commit in a PR.
- Incompatible change (inputs, outputs or behavior of `flow.yml`): major version bump.
