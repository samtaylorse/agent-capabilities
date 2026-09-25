---
name: finish-task
description: Rebase the current task branch on main and merge it into main when explicitly invoked.
---

# Finish Task

1. Confirm the current branch is the task branch and the worktree is clean. Stop if either is unclear.
2. Update local `main` from its upstream with a fast-forward pull, then rebase the task branch onto `main`.
3. Resolve any rebase conflicts. Run relevant verification only if the rebase changes the task branch; stop if it fails.
4. Fast-forward merge the task branch into `main` and leave `main` checked out. Report the result.
