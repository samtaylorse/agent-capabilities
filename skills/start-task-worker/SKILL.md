---
name: start-task-worker
description: Hand off an agreed implementation task from an orchestrator to an isolated worker chat or thread using coding-loop. Use when asked to start a worker for implementation.
---

# Start a Task Worker

The parent orchestrator supplies task context; the worker loads and runs `coding-loop`. Start an isolated worker chat or thread by default. Use a subagent as the implementation worker only when the user explicitly requests one. Use the available harness mechanism to start the worker.

A handoff prompt should name `coding-loop` (and its readable path when it is not discoverable), the task document, repository/worktree, authoritative requirements, ordered subtasks, exclusions, delivery expectations, and explicit approval gates. For example:

> Implement the agreed task in the supplied repository/worktree using the coding-loop skill at [resolved SKILL.md path]. Read [task document] for requirements, acceptance criteria, exclusions, and verification. You own implementation and repair; use a separate code-review subagent. Return the final snapshot or commit, acceptance evidence, verification results, and unresolved decisions.

Ensure the worker can access the skill files and task inputs. The parent retains cross-feature decisions and receives the completion report; the worker maintains implementation details and its task ledger.
