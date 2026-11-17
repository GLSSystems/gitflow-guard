# gitflow-guard

## Table of Contents
- [1. Overview](#1-overview)
- [2. Repository layout](#2-repository-layout)
- [3. Adopting it in a project](#3-adopting-it-in-a-project)
- [4. Workflow](#4-workflow)
- [5. Languages](#5-languages)
- [6. Versions and releases](#6-versions-and-releases)
- [7. Extended documentation](#7-extended-documentation)

## 1. Overview

A GitFlow workflow for GitHub projects where `main` is production and **changes only through a Pull Request merged by an owner account**. It works on GitHub's free plan, including private repositories.

It serves people and automation accounts alike: everyone follows the same flow and everyone stops at the same checkpoint, the PR to `main`.

## 2. Repository layout

| Path | What it is |
|---|---|
| `.github/workflows/flow.yml` | Reusable workflow: PR origin check, `main` protection and integration-branch sync |
| `.github/workflows/ci.yml` | CI of this repository: uses its own flow, validates the files and publishes releases |
| `.githooks/pre-push` | Local hook that blocks direct pushes to `main` |
| `templates/project-ci.yml` | A project's workflow that calls the shared flow, with validation and deploy |
| `templates/pre-push` | Copy of the hook to install in each project |
| `templates/readme-section.<lang>.md` | Section for each project's README |
| `docs/en/` | Extended documentation |
| `CHANGELOG.md` | Versions and their changes |

## 3. Adopting it in a project

Summary; the full steps are in [Adopting the flow in a project](docs/en/adopting.md):

1. Organization and repository settings.
2. `templates/project-ci.yml` as the project's `.github/workflows/ci.yml`, pinned to a version.
3. `MAIN_APPROVERS` repository variable.
4. `templates/pre-push` as `.githooks/pre-push`.
5. README section from `templates/readme-section.<lang>.md`.

## 4. Workflow

Branches `main`, `development`, `feature/*`, `bugfix/*` and `hotfix/*`. Contributors with Write access integrate into the repository's `development`; external contributors and automation accounts integrate into their fork's `development`. Every change reaches `main` through a PR that an owner merges.

Details: [Workflow](docs/en/workflow.md) and [How `main` is protected](docs/en/protection.md).

## 5. Languages

The documentation of this repository is in English. Each project chooses its own language:

| Element | Available languages | How to choose |
|---|---|---|
| Messages of `flow.yml` | `en`, `es` | `language` input in the project's workflow |
| README section | `en`, `es` | `templates/readme-section.<lang>.md` |

Job names stay in English, so check names are the same in every project.

To add a language: add its messages to each `if [ "$LANGUAGE" = ... ]` block in `flow.yml`, add `templates/readme-section.<lang>.md` and update this table. Translated documentation goes in `docs/<lang>/` with the same file names.

## 6. Versions and releases

- Each version is a `## vX.Y.Z` heading at the top of [CHANGELOG.md](CHANGELOG.md), added in the same PR as the change.
- When an owner merges that PR, the **Release** job creates the `vX.Y.Z` tag and the GitHub release with that entry. No manual tagging.
- Projects pin the **commit** of a version, not a branch. A change here affects no project until its owner updates that commit in a PR.
- Incompatible change (inputs, outputs or behavior of `flow.yml`): major version bump.

## 7. Extended documentation

| Document | For |
|---|---|
| [Workflow](docs/en/workflow.md) | Everyone: branches, roles, daily cycle, hotfix, rollback, rules |
| [How `main` is protected](docs/en/protection.md) | Everyone: protection layers and why automation accounts work from a fork |
| [Adopting the flow in a project](docs/en/adopting.md) | Owner: organization and repository setup |
| [Automation accounts](docs/en/automation-accounts.md) | Owner and automation account: fork, credentials and daily work |
| [Verification](docs/en/verification.md) | Owner: checks after setting up or changing anything |
