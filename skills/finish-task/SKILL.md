---
name: finish-task
description: Rebase the current task changes on main and merge them into main when explicitly invoked.
---

# Finish Task

1. Confirm the worktree is clean and identify `HEAD` as the task tip. If it is attached to a branch, record the branch and commit. If `HEAD` is detached, record its commit SHA and continue; creating a task branch is optional. Stop only if the worktree is dirty or the intended task tip cannot be identified.
2. Update local `main` from its upstream with a fast-forward pull, then rebase the task changes onto `main`. `git rebase main` also works from detached `HEAD`.
3. Resolve any rebase conflicts. Run relevant verification if the rebase changes the task tip; stop if verification fails.
4. Record the post-rebase task tip with `git rev-parse HEAD`, then fast-forward merge its branch or commit SHA into `main`. Leave `main` checked out, confirm it contains the task tip, and report the result.
