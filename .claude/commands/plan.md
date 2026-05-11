---
description: Generate a development plan for the next iteration
argument-hint: <task description, e.g. "Implement data preprocessing pipeline">
---

Generate a plan for the task: "$1"

## Grounding Pass

This is **required** before writing.

1. Read `.claude/CLAUDE.md` for project conventions and constraints.
2. Read `STATUS.md` for current phase, recent work, in-progress items, and known issues.
3. Read the most recent 1–2 files in `reports/` to understand prior decisions and any unresolved follow-ups.
4. List `plans/` to determine the next plan ID:
   - Find the highest `NN` and use `{NN+1}` zero-padded (e.g. `03` → `04`).
   - If no plans exist, start with `01`.
5. Inspect every source file you intend to reference in the plan. Do not write tasks targeting code you have not read.
6. If `refs/README.md` points to materials relevant to this task, read them.

## Write the Plan

Write to `plans/NN.<slug>.md` where `<slug>` is lowercase, hyphen-separated, ≤4 words (e.g. `data-preprocessing`, `add-attention-module`).

Use this exact structure:

```markdown
# Plan NN: [Title]

**Created**: YYYY-MM-DD
**Task**: Short summary to the task

## Grounding

[What was found during the grounding pass:
 - Current project state (1–2 lines from STATUS.md)
 - Relevant prior work (reports referenced)
 - Current state of files this plan will touch (paths + 1-line state summary each)
 - Any open issues from STATUS.md that affect this plan]

## Objective

[1–2 sentences: what this iteration achieves and why it matters.]

## Tasks

Concrete, ordered, atomic, verifiable.

- [ ] **Task 1 title**: [What to do, with target file paths]
  - **Acceptance**: [How to verify — test passes, file exists, metric computed, etc.]
- [ ] **Task 2 title**: ...
  - **Acceptance**: ...

[If task order is not strictly linear, add one line at the end describing dependencies. Otherwise omit.]

## Files Expected to Change

| File | Action | Notes |
|---|---|---|
| `src/...` | Create / Modify | [what and why] |
| `tests/...` | Create / Modify | [what and why] |

## Out of Scope

[Explicitly list what is NOT part of this iteration. Required for any plan; write "None" only if truly nothing is excluded.]

## References

- [Files in `refs/` the implementer should read]
- [Prior reports in `reports/` providing relevant context]
- [External: paper sections, doc links]

## Notes for Implementation

[Design guidance, trade-offs, edge cases. Keep brief — the implementer makes detailed decisions and documents them in the report.

If this iteration runs experiments, note any reproducibility requirements: seeds to fix, data version to record, configs to log.]
```

## Rules

- **Be specific**: reference actual file paths, function names, class names from the current codebase. No "improve the model" — say exactly what changes and where.
- **Acceptance criteria are mandatory** on every task. A task without acceptance is not reviewable.
- **Out of Scope is mandatory.** Broad task descriptions invite scope creep; explicit exclusions prevent it.
- **Keep iterations atomic**: one plan = one coherent unit. If the task is too large, split it and note what is deferred.
- **Do not write code**: the plan is a specification. Include code only to clarify an interface contract or expected signature.
- **Do not modify project files** other than the new plan file. No edits to `src/`, `STATUS.md`, or anything else.
- **Surface risks** in Notes for Implementation: dependency conflicts, architectural concerns, unclear requirements.

## After Writing

Print the path of the new plan file and remind the user:

```
Plan written to: plans/NN.<slug>.md
Next: review the plan, then run /dev NN.<slug>
```