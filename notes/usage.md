# Using this `.claude` template

`git@github.com:Yi1-Chen/.claude.git` (branch `project`) is a **reusable Claude-Code scaffolding
template**, shared across all projects. It holds ONLY generic scaffolding. Per-project state lives
with each project, never here.

## Design rule (the whole point)
- **Template = scaffolding only**: `CLAUDE.md` (conventions), `commands/`, `refs/`, `notes/`.
  `STATUS.md`, `reports/`, `plans/` are **gitignored** → never committed to the template.
- **`STATUS.md` is each project's living truth** → it belongs to the **project's own repo**.
- In a project, `.claude/` is a **plain directory** (NO nested `.git`). The project repo tracks
  **only `.claude/STATUS.md`** — the one file that is project-specific and changes. `CLAUDE.md`,
  `commands/`, `refs/` are unchanging template scaffolding: they live on disk (seeded from this
  template) but are **gitignored in the project** (not re-versioned per project). `notes/`, `plans/`,
  `reports/` are local working state, also gitignored.

So: the template is a **source you seed from**, not a live clone nested in each project; and a
project versions only its `STATUS.md`.

## Seed a new project (once)
From the project root (the project's own git repo):
```bash
git clone --depth 1 git@github.com:Yi1-Chen/.claude.git .claude
rm -rf .claude/.git                          # detach: .claude/ is now plain files
# the project tracks ONLY .claude/STATUS.md; everything else stays local:
printf '.claude/*\n!.claude/STATUS.md\n' >> .gitignore
# write .claude/STATUS.md (the project's living status), then:
git add .gitignore .claude/STATUS.md && git commit -m "init .claude: track STATUS.md only"
```

## Sync a template update into a project
The scaffolding is local (gitignored), so this just refreshes the on-disk copy — nothing to commit:
```bash
rsync -a --exclude STATUS.md --exclude reports/ --exclude plans/ --exclude notes/ --exclude .git \
  <a-clean-template-checkout>/ ./.claude/
```

## Why this layout (and not the alternatives)
- **Don't keep `.claude/` as a live clone of this template inside a project.** Then `STATUS.md` either
  pollutes the shared template (if committed) or can't be tracked by the project (nested git repos
  don't compose). Seeding from the template avoids both.
- **Submodule doesn't work**: `STATUS.md` would live in the submodule (= the template) → pollution.
- **Why track only `STATUS.md`?** It is the only project-specific file that changes. `CLAUDE.md` /
  `commands/` / `refs/` are identical across projects (template scaffolding) — versioning a copy in
  every project repo is redundant; source them from the template instead.
- Cost: a fresh clone of a project repo has only `STATUS.md`; re-seed the scaffolding from the
  template (the one-liner above) to use `/dev`, `/plan`, etc.

## What the template repo tracks
`.gitignore`, `CLAUDE.md`, `commands/`, `refs/README.md`, `notes/`. Everything project-specific
(`STATUS.md`, `reports/`, `plans/`) is gitignored so the template stays clean and reusable.

## Pushing to this template
Pushes need write access. On this setup, push via the `git@yi` SSH alias (authenticates as the repo
owner `Yi1-Chen`); plain `git@github.com` is read-only here: `git push git@yi:Yi1-Chen/.claude.git project`.
