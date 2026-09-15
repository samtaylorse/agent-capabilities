---
name: coding-loop
description: Drive a coding task to convergence through fresh-context implementation, independent fixed-base review, batched repair, and verification. Use when a user wants an orchestrated producer-reviewer loop for task details supplied as a prompt, issue, ticket, specification, failing test, or other authoritative input.
---

# Coding Loop

Orchestrate a coding task through produce, review, and repair cycles until the implementation is demonstrably complete. The task input may take any form; a ticket is only one possible specification source.

## Freeze the Contract

Before the first implementation pass, record:

- authoritative task inputs and repository instructions
- observable acceptance criteria, the evidence that will prove each one, and explicit exclusions
- the base commit that review will use as its fixed point
- required verification commands and manual checks
- the worktree, file ownership, and requested commit shape
- the pass limit, defaulting to three producer-reviewer passes

Resolve task inputs into one frozen contract without rewriting their meaning. Before implementation, map every acceptance criterion to a test, inspection, or manual observation that can prove it. Record reasonable assumptions; ask only when an ambiguity would materially change the result. A requirement change starts a new contract rather than silently moving the current review target.

Use one loop for a single task. If the input contains separable work units, order them by dependency and run a loop for each. Parallel work requires isolated worktrees and disjoint file ownership.

Keep a compact ledger of the base commit, current task commit, pass number, findings and dispositions, verification results, and state.

## Produce

Assign a fresh-context producer subagent with the complete contract, relevant source paths, repository instructions, worktree, base commit, owned files, and verification commands. Require the designated implementation skill when the user or environment specifies one.

The producer must:

1. Inspect the affected system and confirm the contract's acceptance evidence is feasible.
2. Implement the smallest coherent change that satisfies the whole contract.
3. Add or update tests for changed behavior when the repository supports them.
4. Run the most relevant checks, then self-review the full `base..HEAD` change for omissions, regressions, and unrelated edits.
5. Create exactly one clean task commit unless the contract requests a different commit shape.

Return the task commit, files changed, acceptance evidence, commands and results, and any residual uncertainty. Review begins only after this handoff is complete.

## Review

Assign a separate fresh-context reviewer subagent with the same contract, repository instructions, worktree, fixed base commit, current task commit, and verification expectations. Require the designated review skill when one is specified.

The reviewer inspects the repository and `base..task` range directly rather than a copied diff. It reviews both specification fit and repository standards, including tests and compatibility requirements.

The reviewer must inspect the full range and every acceptance criterion before returning. Require one complete, deduplicated batch containing either:

- actionable findings with stable IDs, severity, evidence, affected location, violated criterion or invariant, and the condition that would close each finding; or
- an explicit statement that no actionable findings remain.

A finding is actionable when correcting it is necessary for the contract, correctness, security, compatibility, or documented repository standards. Every blocking finding must name the violated criterion or invariant; otherwise label it non-blocking so preferences cannot hold the loop open. Exclude pre-existing defects outside the task range unless the change makes them newly relevant.

## Repair

If actionable findings remain, assign a new fresh-context producer the entire finding batch, the unchanged contract, and the current ledger. The producer resolves the batch as one pass, reruns affected verification, and records one disposition per finding:

- `fixed`, with evidence; or
- `contested`, with concrete evidence that the finding is invalid or outside the contract.

For single-commit delivery, amend the existing task commit and update its SHA. Preserve the original base commit.

Assign a new fresh-context reviewer against that same base. Give it the prior finding IDs and dispositions so it verifies closure, while still reviewing the complete current range for regressions or newly exposed issues. New findings must meet the same actionable bar.

## Converge

The loop is clean only when all of these are true:

- every acceptance criterion has observable evidence
- required automated and manual verification passes
- no actionable review finding remains
- the diff contains no unintended scope or unrelated changes
- the requested commit shape is intact

Run cheap, targeted checks during repair and the full required verification before declaring the loop clean. Distinguish code failures from environmental or flaky failures and record the evidence; retrying a command without a diagnostic does not advance the loop.

Stop after the configured pass limit. If the loop is still open, present unresolved and contested findings, attempted repairs, verification state, and a recommendation, then ask the user to decide rather than continuing indefinitely.

If the user specified human approval gates, stop at each gate, show the requested evidence, and wait for explicit approval before continuing.

Report completion with the fixed base, final task commit, pass count, finding dispositions, and verification evidence.
