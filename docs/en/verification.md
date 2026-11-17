# Verification

Checks for the owner after adopting the flow or changing its setup. Checks that push to `main` are done only from an owner account, at a time with no other changes in progress.

| # | Check | Expected result |
|---|---|---|
| 1 | `git push origin main` from a clone with the hook enabled | Rejected locally |
| 2 | Direct push to `main` skipping the hook (test only) | Reaches GitHub; **Protect main** restores `main`; nothing deploys |
| 3 | PR `feature/x` → `main` | **PR origin** fails |
| 4 | PR `development` → `main` merged by an account not in `MAIN_APPROVERS` | **Protect main** restores `main`; nothing deploys |
| 5 | PR `development` → `main` merged by an owner | **Protect main** passes, the project deploys and **Sync integration branch** updates `development` |
| 6 | Automation account: `git push` to any branch of the original | Rejected (403) |
| 7 | Automation account: `GET /repos/<organization>/<repo>` | `permissions.push: false`, `admin: false` |
| 8 | PR `<account>:development` → `main` | **PR origin** and **Validate** run without access to secrets; an owner merges it and it deploys |
| 9 | Fork PR whose workflow tries to read a secret | The value is empty |
| 10 | Manual run (*Run workflow*) as the automation account | Not available (Read role) |
| 11 | The project's own checks | As defined in its README |
