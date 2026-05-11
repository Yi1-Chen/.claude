---
description: Review an implementation against its plan
argument-hint: <plan-id, e.g. 03 or 03.ablation>
---

Review the report at `reports/$1.md` against its paired plan at `plans/$1.md`. Append a review section to the report.

## Before Reviewing

1. Read `STATUS.md` for the current project state.
2. Read `plans/$1.md` in full, **including all prior review and revision sections**. The plan as appended-and-revised is the contract the report should be measured against.
3. Read `reports/$1.md` end to end.
4. For every file listed in the report's **Files Changed**, read the current content of that file. The report describes intent; the file is the ground truth.
5. If the implementation involves tests, run them: `source .venv/bin/activate && python -m pytest tests/` (or a targeted subset). Verify the report's claims about test results.

## What to Check

**Plan vs report alignment**
- Does every task in the plan have a corresponding outcome in the report? Tasks silently skipped are a blocker.
- Does the report's **Files Changed** match the plan's **Files Expected to Change**? Discrepancies in either direction matter:
  - Files changed but not planned → possible scope creep
  - Files planned but not changed → possible incomplete implementation
- For each acceptance criterion in the plan: is it actually verified by the report, or merely claimed?

**Scope discipline**
- Compare the changes against the plan's **Out of Scope** section. Any out-of-scope work that crept in is a blocker.
- "While I was in there" fixes, formatting churn unrelated to the task, refactors not requested by the plan — these should be in **Follow-ups**, not silently in the implementation.

**Implementation quality**
- Read the actual code. Does it look like a clean implementation of the plan, or are there obvious shortcuts (hardcoded values, suppressed errors, commented-out code, TODO markers in new code)?
- Are there silent regressions in nearby code that wasn't touched but might have been affected?
- Does the implementation follow the project conventions (type hints, `pathlib.Path`, `logging`, etc.)?

**Test integrity**
- The report claims tests pass. Run them and confirm.
- Are there new tests for new behavior, or did the implementation skip test coverage?
- Are any tests skipped, marked `xfail`, or modified to be looser than they were?

**Research integrity**
- Are reported metrics traceable to a specific run, or are they bare numbers without provenance?
- Are negative or null results acknowledged honestly, or smoothed over?

**Key Decisions**
- For each decision logged: is the rationale defensible? Are there alternatives the implementer should have considered?
- Are there decisions that were made implicitly (visible in the code) but not surfaced in the **Key Decisions** section?

## Write the Review

Append a `## Review` section (or `## Review N` if prior reviews exist) to the end of `reports/$1.md`. Use a bullet list ordered by importance — most actionable first. For each finding, state what is wrong (or right) and what to do about it. Note any verification you performed (files read, tests run, commands executed). End with one line on whether the implementation is acceptable, needs revision, or has deeper issues that warrant discussion with the user.

## Do Not

- Edit any prior content of the report or the plan.
- Modify `src/`, `STATUS.md`, or any other file.
- Fix issues yourself — point them out and let the implementer address them in a revision.
- Run `git` commands.