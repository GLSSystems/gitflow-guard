# Workflow

## Table of Contents
- [1. Branches](#1-branches)
- [2. Roles](#2-roles)
- [3. Daily cycle with Write access](#3-daily-cycle-with-write-access)
- [4. Daily cycle from a fork](#4-daily-cycle-from-a-fork)
- [5. Release to production](#5-release-to-production)
- [6. Hotfix](#6-hotfix)
- [7. Rollback](#7-rollback)
- [8. Rules](#8-rules)

## 1. Branches

| Branch | Purpose | Created from | Merges into | Deploys |
|---|---|---|---|---|
| `main` | What runs in production | — | — | Yes |
| `development` | Integrates the next release | `main` | `main`, via PR | No, validates only |
| `feature/<topic>` | New functionality or content | `development` | `development` (`--no-ff`) | No |
| `bugfix/<topic>` | Fix for the release in progress | `development` | `development` (`--no-ff`) | No |
| `hotfix/<topic>` | Urgent production fix | `main` | `main`, via PR | When its PR is merged |

Branch names: lowercase, words separated by hyphens (`feature/contact-form`).

After each accepted merge into `main`, the shared flow brings `main` into the repository's `development` (fast-forward or merge). On conflict it warns, and someone with Write access resolves it.

## 2. Roles

| Role | Repository role | Integrates in | Can merge into `main` |
|---|---|---|---|
| Owner | Admin, login in `MAIN_APPROVERS` | Repository branches | Yes: the checkpoint |
| Contributor | Write | The repository's `development` | No |
| External contributor or automation account | Read, plus its own fork | Its fork's `development` | No |

Merging a PR into `main` is the approval. Only merges by a login in `MAIN_APPROVERS` count; any other change to `main` is reverted.

Automation accounts always work from a fork. The reason is in [How `main` is protected](protection.md#3-why-automation-accounts-use-a-fork).

## 3. Daily cycle with Write access

```sh
git checkout development && git pull
git checkout -b feature/<topic>           # or bugfix/<topic>
# changes, commits and local review
git checkout development && git pull
git merge --no-ff feature/<topic>
git push origin development               # CI validates; nothing is deployed
git branch -d feature/<topic>
```

## 4. Daily cycle from a fork

The clone has two remotes: `origin` (the fork) and `upstream` (the original repository). Setup in [Automation accounts](automation-accounts.md#5-working-clone).

```sh
git fetch upstream
git checkout development && git merge upstream/main      # bring in what was released
git checkout -b feature/<topic>                          # or bugfix/<topic>
# changes, commits and local review
git checkout development
git merge --no-ff feature/<topic>
git push origin development
git branch -d feature/<topic>
```

When the fork's `development` is ready: open a PR `<fork>:development` → `main` of the original repository.

## 5. Release to production

1. Open a PR to `main` from `development` (of the repository or the fork) with a summary of the changes and how to verify them. Set `main` as the base explicitly.
2. Wait for **PR origin** and **Validate** to pass.
3. On conflicts: merge `main` into the source `development`, resolve and push. The PR updates itself.
4. An owner reviews the whole PR, especially any change in `.github/workflows/`, and merges it with **Create a merge commit**.
5. **Protect main** accepts the merge; the project deploys and **Sync integration branch** updates the repository's `development`.
6. Fork users bring `main` into their `development` before continuing (`git fetch upstream && git merge upstream/main`).

## 6. Hotfix

```sh
git fetch upstream                           # from a fork; with Write: git checkout main && git pull
git checkout -b hotfix/<topic> upstream/main # with Write: git checkout -b hotfix/<topic>
# fix and commit
git push -u origin hotfix/<topic>
```

PR `hotfix/<topic>` → `main`. An owner merges it and it deploys. **Sync integration branch** brings the fix into the repository's `development`.

## 7. Rollback

Release a `git revert` of the change on a `hotfix/*` branch from `main` and merge its PR. Protection and deploy run as usual. Never move `main` by hand.

## 8. Rules

Never:

- push, force-push, rebase or reset `main`;
- force-push the repository's `development`;
- merge a PR into `main` unless your login is in `MAIN_APPROVERS`;
- use `git push --no-verify`;
- commit credentials (`.env`, keys, tokens).

If **Protect main** reverts a change, do not push it again: open a PR.
