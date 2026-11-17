# Adopting the flow in a project

Steps for the owner. Done once per organization and once per repository.

## Contents

- [1. Organization](#1-organization)
- [2. Repository](#2-repository)
- [3. Project workflow](#3-project-workflow)
- [4. Local hook](#4-local-hook)
- [5. Project README](#5-project-readme)
- [6. Updating the shared flow version](#6-updating-the-shared-flow-version)

## 1. Organization

In the organization's **Settings**:

| Where | What |
|---|---|
| Member privileges → Repository forking | Enable *Allow forking of private repositories* (needed when accounts work from a fork) |
| Actions → General → Policies | Allow the reusable workflow: *Allow all actions and reusable workflows*, or add `GLSSystems/gitflow-guard/.github/workflows/flow.yml@*` to the allow list |
| Actions → General → Fork pull request workflows in private repositories | Enable *Run workflows from fork pull requests*. **Disable** *Send write tokens to workflows from fork pull requests* and *Send secrets and variables to workflows from fork pull requests*. Optional: *Require approval for fork pull request workflows* |
| Personal access tokens → Settings | Allow *fine-grained personal access tokens* (for automation accounts that work from a computer) |

## 2. Repository

In the repository's **Settings**:

| Where | What |
|---|---|
| General | Enable *Allow forking* |
| General → Pull Requests | Enable *Allow merge commits* |
| Collaborators and teams | Owners: Admin. Trusted contributors: Write. Automation accounts and external contributors: **Read** |
| Actions → General → Fork pull request workflows | Same values as in the organization |
| Secrets and variables → Actions → Variables | `MAIN_APPROVERS`: GitHub logins allowed to merge into `main`, comma-separated. Without it, the protection rejects everything and nothing deploys |

Deploy variables and secrets: at repository level. Private repositories on the free plan have no environments with protection rules.

## 3. Project workflow

1. Copy [templates/project-ci.yml](../../templates/project-ci.yml) to `.github/workflows/ci.yml`.
2. Replace `<SHA>` with the commit of the chosen version (see [CHANGELOG.md](../../CHANGELOG.md)) and note the version in the comment.
3. Set `language` (`en`, `es`) for messages.
4. Fill in `validate` with the project's checks.
5. Fill in `deploy` with the deploy steps. Keep its `if` condition: it prevents deploying a rejected change.
6. If the repository already had a workflow with its own `main` protection, replace it in the same PR so two protections do not run.

The change enters the repository like any other: branch, `development`, PR to `main` merged by an owner.

## 4. Local hook

Copy [templates/pre-push](../../templates/pre-push) to `.githooks/pre-push` (executable). Each person enables it once per clone:

```sh
git config core.hooksPath .githooks
```

## 5. Project README

Add the section from `templates/readme-section.<lang>.md` ([en](../../templates/readme-section.en.md), [es](../../templates/readme-section.es.md)). The project's README documents only what is specific to it: what it validates, how it deploys, its infrastructure and its variables. The flow is linked, not copied.

## 6. Updating the shared flow version

1. Read the new version's entry in [CHANGELOG.md](../../CHANGELOG.md).
2. Change the pinned commit in the project's `.github/workflows/ci.yml`.
3. Bring it to `main` through the normal flow and repeat the [verification](verification.md).
