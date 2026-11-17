# Automation accounts

An automation account is a GitHub account used by a tool (bot, external CI, assistant) to work on the repository. It follows the same flow as a person and always works from a fork. The reason is in [How `main` is protected](protection.md#why-automation-accounts-use-a-fork).

## Contents

- [What it can and cannot do](#what-it-can-and-cannot-do)
- [Setting up the account](#setting-up-the-account)
- [Fork](#fork)
- [Credentials](#credentials)
- [Working clone](#working-clone)
- [Daily work](#daily-work)
- [Troubleshooting](#troubleshooting)

## What it can and cannot do

| Can | Cannot |
|---|---|
| Read the original repository | Write to the original repository: branches, tags, workflows, settings |
| Create branches, integrate into `development` and change any file in its fork | Merge into `main` or deploy |
| Open PRs to `main`, comment on them and update them | Approve its own PR or run workflows manually |
| Read the check status of its PRs | Read secrets (fork PR workflows run without them) |

## Setting up the account

Done by the owner:

1. Create a GitHub account for the automation, with its own email and two-factor authentication.
2. Invite it to the repository with the **Read** role (Settings → Collaborators and teams).
3. If the organization requires approving tokens, approve the account's tokens when it creates them.

Do not give the account Write or Admin, and do not add it to `MAIN_APPROVERS`.

## Fork

Signed in as the automation account:

1. In the original repository: **Fork** → owner: the automation account. Copy all branches (uncheck *Copy the main branch only*).
2. In the fork: Settings → Actions → General → disable Actions. CI runs in the original repository, on the PR.

A fork of a private repository is private and is deleted if the account loses access to the original.

## Credentials

Working from a computer needs two fine-grained tokens, because each token has a single resource owner:

| Token | Resource owner | Repositories | Permissions | Use |
|---|---|---|---|---|
| Fork | The automation account | Its fork | Contents: Read and write | `git push` to the fork |
| Original | The organization | The original repository | Contents: Read · Pull requests: Read and write | `git fetch`, open and comment on PRs |

*Metadata: Read-only* is mandatory in both. No other permission. Note the expiration date and renew them in time.

An integration that provides its own credential (a GitHub App, for example) replaces these tokens; the Read role still limits what it can do in the original.

On a shared computer, store each token only for its repository path, so the account cannot act as a person:

```sh
git config --global credential.useHttpPath true
```

Store each token with `git credential approve` (`protocol=https`, `host=github.com`, `path=<owner>/<repo>.git`, `username=<login>`, `password=<token>`), without leaving it in the shell history. Remove personal GitHub credentials from that computer.

## Working clone

```sh
git clone https://github.com/<account>/<repo>.git
cd <repo>
git remote add upstream https://github.com/<organization>/<repo>.git
git config user.name "<account login>"
git config user.email "<account email>"
git config core.hooksPath .githooks
git fetch upstream
```

## Daily work

Follow the [daily cycle from a fork](workflow.md#daily-cycle-from-a-fork) and open the PR `<account>:development` → `main` when a release is requested.

Opening a PR through the API (with the original repository's token):

```sh
curl -s -X POST -H "Authorization: Bearer $TOKEN" -H "Accept: application/vnd.github+json" \
  https://api.github.com/repos/<organization>/<repo>/pulls \
  -d '{"title":"<title>","head":"<account>:development","base":"main","body":"<summary and how to verify>"}'
```

After the merge: `git fetch upstream && git checkout development && git merge upstream/main && git push origin development`.

## Troubleshooting

| Problem | Cause | Fix |
|---|---|---|
| `git push` to the original returns 403 | Expected: Read role | Push to the fork (`origin`) |
| `git push` to the fork returns 403 | Fork token expired or without *Contents: Read and write* | Renew it with that permission |
| Creating the PR returns 403 or 404 | Original token without *Pull requests: Read and write*, expired or pending approval | Check the token; approve it in the organization |
| The PR shows no checks | The organization does not run fork workflows, or requires approving them | Enable *Run workflows from fork pull requests*; the owner approves the run |
| The PR shows already-released commits | The fork's `development` lacks `main` | `git fetch upstream && git merge upstream/main` on `development` and push |
| No Fork button | The organization or repository does not allow forking private repositories | Enable it ([Adopting the flow](adopting.md#1-organization)) |
