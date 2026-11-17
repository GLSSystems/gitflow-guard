# How `main` is protected

## Table of Contents
- [1. Goal](#1-goal)
- [2. Layers](#2-layers)
- [3. Why automation accounts use a fork](#3-why-automation-accounts-use-a-fork)
- [4. What it does not cover](#4-what-it-does-not-cover)

## 1. Goal

Every change to `main`, and therefore every deploy, goes through a PR merged by an owner account. No other account, person or automation, can change `main` or deploy.

On GitHub's free plan, private repositories do not enforce branch protection rules or rulesets. These layers replace them.

## 2. Layers

| # | Layer | Where | Prevents |
|---|---|---|---|
| 1 | Read-only role for automation accounts and external contributors | Repository settings | Any write to the repository: branches, workflows, tags |
| 2 | Fork PR workflows without secrets and with a read-only token | Actions settings | Code from an external PR deploying or reading credentials |
| 3 | **Protect main** (shared flow) | Actions, on every change to `main` | Changes to `main` that are not a PR merged by an approver: reverts them, nothing deploys |
| 4 | Deploy gated by the `main-accepted` output | Project workflow | Deploying a change that layer 3 rejected |
| 5 | Owner review | PR to `main` | Merging code, workflows included, without review |
| 6 | `pre-push` hook | Each clone | Accidental pushes to `main` (can be skipped; hence layers 1 to 5) |

For a few seconds after a rejected push, `main` holds that commit. Nothing deploys in that window: deploy waits for layer 3.

## 3. Why automation accounts use a fork

**Protect main** runs from the workflow contained in the very commit that reaches `main`. An account that can push to `main` **and** modify workflows can replace the protection in that same push.

A token without the *Workflows* permission prevents the second part, but not every credential allows that. An integration such as a GitHub App, for example, may include that permission with no way to remove it. So the control does not depend on the kind of credential but on the **role**:

- With the Read role, the account cannot write anything to the repository, whatever its credential. Effective access is the intersection of role and credential permissions.
- It works in its fork, where it can write: branches, `development`, even workflows.
- Everything it proposes arrives as a PR. Its workflows run without secrets (layer 2) and an owner reviews them before merging (layer 5).

People with the Write role could also replace the protection. Trusting them is the owner's decision: keep the list of Write accounts short.

## 4. What it does not cover

- **Manual runs** (*Run workflow* in the Actions tab): they redeploy `main` without the protection. Only accounts with write access to the repository can start them; that is why automation accounts have the Read role.
- **Compromised owner accounts**: whoever controls an account in `MAIN_APPROVERS` can merge anything. Protect those accounts with two-factor authentication.
- **GitHub Team plan or higher**: if the organization gets it, add native rulesets for `main` and `development` as an extra layer and keep the others.
