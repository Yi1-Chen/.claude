# Using this `.claude` template

`git@github.com:Yi1-Chen/.claude.git` (branch `project`) is a **reusable Claude-Code scaffolding
template**, shared across all projects. It holds ONLY generic scaffolding. Per-project state lives
with each project, never here.

## Design rule (the whole point)
- **Template = scaffolding only**: `CLAUDE.md` (conventions), `commands/`, `refs/`, `notes/`.
  `STATUS.md`, `reports/`, `plans/` are **gitignored** → they are never committed to the template.
- **`STATUS.md` is each project's living truth** → it belongs to the **project's own repo**, not here.
- In a project, `.claude/` is a **plain directory tracked by that project's repo** (NO nested `.git`).
  Its `STATUS.md` is versioned with the project; `reports/` and `plans/` stay local.

So: the template is a **source you copy from**, not a live clone nested inside each project.

## Seed a new project (once)
From the project root (the project's own git repo):
```bash
git clone --depth 1 git@github.com:Yi1-Chen/.claude.git .claude
rm -rf .claude/.git                                  # detach: .claude/ is now the project's plain files
printf '.claude/reports/\n.claude/plans/\n' >> .gitignore
# write .claude/STATUS.md (the project's living status), then:
git add .claude .gitignore && git commit -m "init .claude from template + project STATUS"
```

## Sync a template update into a project (never touches project state)
```bash
rsync -a --exclude STATUS.md --exclude reports/ --exclude plans/ --exclude .git \
  <a-clean-template-checkout>/ ./.claude/
git add .claude && git commit -m "sync .claude template"
```

## Why this layout (and not the alternatives)
- **Don't keep `.claude/` as a live clone of this template inside a project.** Then `STATUS.md` either
  pollutes the shared template (if committed) or can't be tracked by the project (nested git repos don't
  compose). Treating the template as a copy-source avoids both.
- **Submodule doesn't work**: `STATUS.md` would live in the submodule (= the template) → pollution.
- **A separate path (e.g. `docs/STATUS.md`) works** but moves the status away from `.claude/STATUS.md`,
  breaking the convention CLAUDE.md relies on. Prefer keeping it at `.claude/STATUS.md`.
- Cost of this layout: template updates propagate by the `rsync` above (one line), not `git pull`.
  Worth it to keep the template pure and each project's `STATUS.md` at `.claude/STATUS.md`.

## What the template repo tracks
`.gitignore`, `CLAUDE.md`, `commands/`, `refs/README.md`, `notes/`. Everything project-specific
(`STATUS.md`, `reports/`, `plans/`) is gitignored so the template stays clean and reusable.
