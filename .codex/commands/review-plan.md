---
description: Review a plan before implementation
argument-hint: <plan-id, e.g. 03 or 03.ablation>
---

Review the plan at `plans/$1.md` and append a review section.

## Before Reviewing

1. Read `STATUS.md` to understand the current project state and any open issues.
2. Read `plans/$1.md` end to end, including any prior review or revision sections.
3. Skim the most recent 1–2 files in `reports/` if the plan references prior work.
4. For every source file the plan claims to touch, **read the current state of that file**. A plan's claims about existing code are the first thing to verify.
5. If the plan references items in `refs/`, read them.

## What to Check

Walk through these dimensions. Not every dimension yields a finding — silence on a dimension means it looked fine.

**Grounding accuracy**
- Does the plan's Grounding section match what is actually in the repo? Wrong file paths, stale assumptions about code state, and references to deleted/renamed entities are the most damaging errors at this stage.
- Are open issues from `STATUS.md` acknowledged where relevant?

**Task quality**
- Is each task atomic and self-contained, or does it bundle multiple concerns?
- Is each acceptance criterion measurable? "Model works" is not; "validation loss < 0.3 on the held-out set" is.
- Are task descriptions specific enough that someone could implement them without re-doing the planner's research?

**Scope discipline**
- Is the Out of Scope section present and meaningful? An empty Out of Scope on a broad task is a red flag.
- Do any of the tasks quietly include work that should have been excluded?
- Does the plan respect the "one plan = one coherent unit" rule, or is it trying to do too much?

**Feasibility and risk**
- Are there dependencies on data, models, or external services that aren't acknowledged?
- Are there architectural choices being made implicitly that deserve to be surfaced?
- For research code: if experiments are involved, does the plan note reproducibility requirements (seeds, data version, configs to log)?

**Internal consistency**
- Do the Files Expected to Change align with the Tasks?
- Do the References cited actually contain what the plan claims they contain?

## Write the Review

Append a `## Review` section (or `## Review N` if prior reviews exist) to the end of `plans/$1.md`. Use a bullet list ordered by importance — most actionable first. For each finding, state what is wrong (or right) and what to do about it. Note any verification you performed (files read, etc.). End with one line on whether the plan is ready to proceed, needs changes, or has deeper issues.

## Do Not

- Edit any prior content of the plan.
- Modify `src/`, `STATUS.md`, or any other file.
- Rewrite the plan yourself — point out what needs to change and let the implementer revise.
- Run `git` commands.