---
name: coding-loop
description: Deliver a coding task through implementation in the current worker chat, independent review, batched repair, and verification. Use when explicitly asked to run the coding loop on a prompt, issue, ticket, specification, or failing test.
disable-model-invocation: true
---

# Coding Loop

The chat running this skill owns implementation and repair. It may be an isolated task worker chat or thread started by a long-lived orchestrator, or the user's current chat. Use `tdd` for implementation and a separate reviewer subagent using `code-review` for independent review. If implementation delegation is justified by task size, independent work streams, or context recovery, prefer isolated worker chats or threads; use implementation subagents only when explicitly requested by the user.

## Establish the Task

Resolve `tdd` and `code-review`, preferring explicit skill paths supplied by the caller, then readable sibling skill folders in this collection, then available skill metadata. Resolve `git-commit` the same way when delivery requires commits. Check availability from metadata or supplied paths without preloading dependency bodies or scanning every installed skill. Load each dependency when its phase begins. If a required dependency is inaccessible, report it before changing code. Record the resolved paths so delegated reviewers use the intended versions.

Inspect the affected system, then record a compact task contract:

- authoritative inputs, repository instructions, observable acceptance criteria, and exclusions
- evidence for each criterion, test seams, verification commands, and manual checks
- fixed review base, worktree, owned files, and requested delivery or commit shape
- pass limit, defaulting to three implementation-review passes

Keep seams provisional until inspection establishes feasibility; follow `tdd` for seam selection and any required user agreement. Record reasonable assumptions and ask only when ambiguity materially changes the result. Requirement changes create a new contract revision and explicitly identify affected evidence and review scope.

Store the contract and compact ledger in a task document at a repository-approved location, reusing an existing task file when possible. Record the base, current snapshot, pass, finding dispositions, verification evidence, and next action so another session can resume. Keep workflow notes out of the deliverable unless requested or conventional for the repository.

Use one loop for a single task. If the input contains separable work units, order them by dependency and run a loop for each. Parallel implementation requires isolated worktrees and disjoint file ownership.

## Implement

Load `tdd`, implement the smallest coherent change, and run focused tests and relevant static checks after coherent slices. When a criterion cannot be exercised test-first, record why and provide other observable evidence. Run required automated and manual verification before review, then self-review the complete task snapshot using `code-review`'s scope rules.

Keep implementation and repair in this worker so knowledge carries across passes. Checkpoint commits are optional unless the contract requires them; preserve the requested final commit shape for delivery, defaulting to one clean task commit when commits are part of delivery. Load `git-commit` before creating or amending a commit. Amend only task-owned commits, preserving the original base.

## Review and Repair

Freeze edits to the review snapshot. Launch one fresh-context reviewer subagent with the resolved `code-review` path, contract, repository instructions, worktree, base, snapshot identification, and verification expectations. Supply minimal implementation narrative. The reviewer follows `code-review` for scope, evidence, findings, and re-review; this skill owns only the scheduling and state transitions. Additional focused reviewers are optional when complexity or risk warrants them.

Resolve the returned batch in this worker. Record each finding as fixed with evidence, contested with concrete evidence, or still open. Optional suggestions do not block convergence. Run affected checks after repairs and dispatch a fresh reviewer with the same base, updated snapshot, and previous IDs and dispositions. A contest remains unresolved until the reviewer accepts its evidence or the user adjudicates it.

## Converge and Deliver

Declare the loop clean only when every acceptance criterion has evidence, required verification passes, no blocking finding remains, and the deliverable has no unintended changes. Verification results apply to the snapshot checked: rerun affected verification and review when subsequent edits change behavior. Reuse results when only commit metadata changes and the reviewed tree is identical.

Load `git-commit` if commits are requested, finalize the delivery shape, and confirm it preserves the reviewed content. Distinguish code failures from environmental or flaky failures; diagnose before retrying. Stop at the pass limit with unresolved findings, attempted repairs, verification state, and a recommendation for the user. Respect any explicit human approval gates.

Update the task document and return the base, final snapshot or task commit, pass count, finding dispositions, acceptance evidence, verification results, and unresolved decisions to the user or parent orchestrator.
