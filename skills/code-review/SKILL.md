---
name: code-review
description: Review a branch, commit range, PR, or working-tree changes for correctness, specification fit, tests, and repository standards. Use for independent review or self-review within a coding workflow.
---

# Code Review

Review the supplied task snapshot directly in the repository. One reviewer covers all review areas by default. The caller may assign additional focused reviewers for a large or high-risk change; a reviewer completes its assigned scope itself rather than creating another coordinator layer.

## Establish the Review Inputs

Use the supplied task contract or prompt as the primary specification, followed by explicitly linked specs or issues. Consult related repository documents when they add necessary context. An issue tracker is optional. Ask only when missing or conflicting requirements materially prevent review; otherwise record assumptions and any limits on specification coverage.

Read applicable repository instructions and standards, including `CODING_STANDARDS.md` and `CONTRIBUTING.md` when present, and relevant design decisions.

For independent review, request the contract, source paths, snapshot, and verification expectations. Prefer a fresh context with those inputs over inheriting the implementation conversation. Inspect the code and evidence independently; implementation summaries and passing tests are claims to check, not proof of correctness. Review is read-only: return findings for the implementer to resolve.

## Pin the Review Snapshot

Record the repository/worktree, review mode, task scope, and resolved base commit. Resolve symbolic refs once with `git rev-parse --verify <ref>^{commit}` and use the resulting SHAs throughout. When the caller requests a merge-base comparison, compute and record that base once with `git merge-base <base-ref> <tip-sha>`; otherwise use the supplied base directly. Ask when the intended base is ambiguous.

- **Committed review:** resolve the task tip to a SHA and inspect `git diff <base-sha> <tip-sha>` and the relevant commit history. Review files at that tip, using a matching checkout or Git object reads; the current working tree may contain different code. Report uncommitted changes as outside this snapshot.
- **Working-tree review:** record the starting HEAD SHA and status. Inspect `git diff <base-sha>` for the complete tracked change through the current working tree, including committed task changes, staged changes, and unstaged changes. Use `git diff --cached` and `git diff` when needed to understand partial staging. Enumerate untracked files with `git ls-files --others --exclude-standard` and read those relevant to the task. Explicitly account for unrelated existing changes without modifying them. Review the intended final content; staged content alone is a separate scope that must be explicitly requested.

Keep the snapshot stable during review. For working-tree review, record a content fingerprint of the tracked diff and relevant untracked files, then compare it and HEAD/status again before returning. Status alone cannot detect content edits. If the snapshot changes, mark the review stale and review the updated snapshot before approving it. Record which snapshot verification commands actually exercised.

An empty diff is an observation, not a successful review: check whether the contract requires missing work, whether the wrong mode excluded uncommitted files, or whether no change was intended. Report the conclusion and any coverage limits.

## Review the Complete Change

Inspect the complete assigned change and surrounding callers, dependencies, and tests needed to assess its effects. Account for every acceptance criterion, including missing code or tests that cannot appear in a diff hunk.

- **Correctness and compatibility:** trace changed behavior, error paths, edge cases, state transitions, data handling, and relevant security, concurrency, and performance risks. Check affected callers and contracts even when no specification sentence names the invariant.
- **Specification fit:** identify missing or partial requirements, incorrect behavior, and unintended scope. Map acceptance criteria to actual evidence.
- **Tests and verification:** assess whether tests can detect the intended failure, cover material regressions, and avoid coupling to incidental implementation details. Run relevant checks when useful; distinguish observed results from reported results and disclose checks you could not run.
- **Repository standards and maintainability:** cite documented rules for violations. Apply the smell baseline below. Report a heuristic only when it has a concrete consequence worth addressing; repository conventions take precedence.

Prioritize defects introduced or made relevant by this change. Existing unrelated defects and hypothetical preferences do not block delivery. Tool-enforced formatting belongs in check results rather than repeated inline findings.

## Smell Baseline

On top of whatever the repo documents, the Standards axis always carries the **smell baseline** below: a fixed set of Fowler code smells (_Refactoring_, ch.3) that applies even when a repo documents nothing. Two rules bind it:

- **The repo overrides.** A documented repo standard always wins; where it endorses something the baseline would flag, suppress the smell.
- **Always a judgement call.** Each smell is a labelled heuristic ("possible Feature Envy"), never a hard violation. Like any standard here, skip anything tooling already enforces.

Each smell reads *what it is* → *how to fix*; match it against the diff:

- **Mysterious Name**: a function, variable, or type whose name doesn't reveal what it does or holds. → rename it; if no honest name comes, the design's murky.
- **Duplicated Code**: the same logic shape appears in more than one hunk or file in the change. → extract the shared shape, call it from both.
- **Feature Envy**: a method that reaches into another object's data more than its own. → move the method onto the data it envies.
- **Data Clumps**: the same few fields or params keep travelling together (a type wanting to be born). → bundle them into one type, pass that.
- **Primitive Obsession**: a primitive or string standing in for a domain concept that deserves its own type. → give the concept its own small type.
- **Repeated Switches**: the same `switch`/`if`-cascade on the same type recurs across the change. → replace with polymorphism, or one map both sites share.
- **Shotgun Surgery**: one logical change forces scattered edits across many files in the diff. → gather what changes together into one module.
- **Divergent Change**: one file or module is edited for several unrelated reasons. → split so each module changes for one reason.
- **Speculative Generality**: abstraction, parameters, or hooks added for needs the spec doesn't have. → delete it; inline back until a real need shows.
- **Message Chains**: long `a.b().c().d()` navigation the caller shouldn't depend on. → hide the walk behind one method on the first object.
- **Middle Man**: a class or function that mostly just delegates onward. → cut it, call the real target direct.
- **Refused Bequest**: a subclass or implementer that ignores or overrides most of what it inherits. → drop the inheritance, use composition.

## Return One Finding Batch

Return a concise, deduplicated batch, ordered by severity, with category labels retained. Include all material findings without a fixed word cap. Each finding includes:

- stable ID, category, severity, and `blocking` or `optional`
- affected file and precise location, or the missing behavior's expected location
- concrete evidence and the trigger, consequence, and violated requirement or invariant
- the observable condition or verification that would close it

A finding blocks delivery when correction is necessary for the contract, correctness, security, compatibility, or documented repository standards. Suggestions without such a requirement or invariant are optional and cannot hold the loop open. Express uncertainty and request targeted evidence when a suspected defect cannot yet be established.

On re-review, preserve prior IDs, verify each proposed fix or evidence-backed contest, and report its disposition. Review the complete updated scope for regressions and newly exposed issues; apply the same bar to new findings.

Finish with snapshot identification, coverage and verification limits, and an explicit statement of whether blocking findings remain. If none remain, say so while listing optional suggestions separately. Approval describes the reviewed evidence and scope; it is not a guarantee of correctness.
