---
description: Execute a development plan by ID
argument-hint: <plan-id, e.g. 03 or 03.ablation>
---

Execute the plan at `plans/$1.md`.

## Before Starting

1. Read `STATUS.md` for current state and open issues.
2. Read `plans/$1.md`.
3. If the plan references items in `refs/` or earlier `reports/`, read them.

## During Execution

- Follow the plan's task order unless a dependency forces reordering.
- Run affected tests after each meaningful change. **If tests fail, fix before writing the report** — never report a `/dev` with failing tests as a success.
- For under-specified requirements, make a reasonable decision and document it in **Key Decisions**. Do not pause to ask.
- **Stay in scope.** The plan's Out of Scope section is binding. Discoveries that fall outside scope go to **Follow-ups**, never silently into the implementation.
- Do not touch `refs/`. Do not run `git` commands.

## After Completion

Three steps, in order: write the report, update `STATUS.md`, send the notification.

### 1. Write the Report

Write to `reports/$1.md` using this template:

```markdown
# Report: $1

**Date**: YYYY-MM-DD
**Plan**: `plans/$1.md`

## Summary
[2–3 sentences: goal and outcome.]

## Files Changed
- `src/path/to/file.py` — [one-line description of change]
- ...

## Environment Changes
[New dependencies installed, `pyproject.toml` updates, dataset/checkpoint additions. Otherwise "None".]

## Key Decisions
- [Decisions not specified in the plan, with rationale]
- [Trade-offs made]

## Results
[Experiment metrics with exact numbers, or "N/A — no experiments in this iteration".]

## Issues & Observations
- [Bugs found, technical debt, performance concerns, unexpected behavior]

## Follow-ups
- [ ] [Concrete next actions, including any out-of-scope discoveries from this cycle]
```

### 2. Update STATUS.md

1. **Last Updated** — today's date, note the plan ID (e.g., "after 03.ablation").
2. **Current Phase** — update if the project entered a new phase.
3. **Recently Completed** — append a 1–2 line summary. Keep the last 3 entries; remove older ones.
4. **In Progress** — reflect active work, or leave empty if awaiting the next plan.
5. **Next Steps** — replace with the report's Follow-ups.
6. **Known Issues** — add new ones; remove resolved ones.

### 3. Send Telegram Notification

Compose a terse summary (2–4 short lines, ≤500 chars, markdown allowed): goal, key result, anything the user should know. The notification supplements the report — it is not a substitute, so do not duplicate the full report.

Then run, with `<SUMMARY>` replaced by your text (escape backticks and special chars for shell):

```bash
source ~/.config/claude-notify.env && \
PROJECT=$(basename "$(pwd)") && \
TMUX_SESSION=$(tmux display-message -p '#S' 2>/dev/null || echo "no-tmux") && \
HOST=$(hostname -s) && \
TIMESTAMP=$(date '+%Y-%m-%d %H:%M:%S') && \
MESSAGE="✅ *Claude Code Task Done*
🖥 \`${HOST}\` · 📁 \`${PROJECT}\` · 🪟 \`${TMUX_SESSION}\`
🕐 ${TIMESTAMP}
📄 \`reports/$1.md\`

<SUMMARY>" && \
curl -s -X POST "https://api.telegram.org/bot${TG_BOT_TOKEN}/sendMessage" \
  -d "chat_id=${TG_CHAT_ID}" \
  -d "parse_mode=Markdown" \
  --data-urlencode "text=${MESSAGE}" > /dev/null \
  && echo "Notification sent." || echo "Notification failed (continuing)."
```

Notification failure does not block the cycle — the report and `STATUS.md` are the source of truth.

## Writing Rules

- Reports are **append-only**. Once written, do not edit prior content. Future revisions go in `## Revision N` sections.
- Be factual and specific: file paths, exact metric values, concrete error messages.
- Do not inflate results or gloss over problems. A report that someone reads three months later with no context should still be trustworthy.
- Do not self-assess as "looks good" or "successful" — describe what was done; judgment is handled separately.