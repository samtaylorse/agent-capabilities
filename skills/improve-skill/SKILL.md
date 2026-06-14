---
name: improve-skill
description: Review the most recent use of a skill and improve that skill when the run revealed reusable lessons, missing instructions, confusing triggers, brittle workflow steps, validation gaps, or unnecessary friction. Use when the user asks whether anything learned from a recent skill run should be folded back into the skill, asks to improve/update/refine a skill after using it, or invokes an "improve skill" retrospective flow.
---

# Improve Skill

Use this skill to turn fresh experience from a skill run into a small, useful skill improvement.

## Workflow

1. Identify the skill that was just used and locate its folder.
2. Reconstruct what happened from the current conversation, tool outputs, diffs, logs, generated artifacts, and any user feedback.
3. Look for reusable lessons, not one-off outcomes:
   - instructions the agent had to infer but should have been told
   - setup, validation, or cleanup steps that were missing
   - trigger wording that was too broad, too narrow, or ambiguous
   - resource files that would reduce repeated context or repeated code
   - failure modes, edge cases, or safety constraints worth preserving
   - steps that caused unnecessary questions, approvals, or churn
4. Read the current `SKILL.md` and any directly relevant referenced files before editing.
5. Decide whether to edit:
   - Edit when the lesson is likely to help future runs of the same skill.
   - Do not edit for project-specific facts, temporary environment details, or conclusions that only apply to the current artifact.
   - Ask the user before changing the skill's scope, adding dependencies, or making a large restructuring.
6. Make the smallest improvement that captures the lesson.
7. Validate the skill using the repository's available validator when one exists.
8. Summarize what changed and why. If no edit was useful, say so and name the reason.

## Editing Guidelines

Keep improvements compact. Prefer sharpening existing instructions over adding new sections.

Preserve portability unless the skill is intentionally harness-specific. Put harness-specific details in the relevant adapter or metadata file when the repo has that pattern.

Keep `SKILL.md` focused on reusable procedural knowledge. Add scripts, references, or assets only when they reduce repeated work or make future runs more reliable.

Avoid storing:

- private conversation details
- transient file paths or local-only state
- task results that are not reusable
- long postmortems or changelogs
- broad advice the base agent already knows

## Validation

After editing, run the skill validator if available. For Codex-style skills created with the system skill tooling, use:

```bash
python <skill-creator>/scripts/quick_validate.py <path-to-skill-folder>
```

If validation cannot be run, report why and do a manual check that frontmatter is valid, the skill name matches the folder, and the description clearly states when to use the skill.
