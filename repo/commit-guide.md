# Commit guide

Instructions for Claude (and the human) on when and how to commit in this repo. `repo/` is the
only git-tracked directory in this project (see `~/Documents/project-context/project-skeleton.md`
§1) — `context/` (including `context/misc/`) sits outside its history entirely.

---

## When to commit

Commit at meaningful state changes, not at every file edit. Good triggers:

- A pipeline stage is added or reworked (a new ingest/preprocessing/EDA script lands and runs clean)
- A bug fix that changes output — especially anything touching a data-quality invariant
- `config/settings.yaml` changes that shift what a run produces
- A batch of related script edits that belong together as one reviewable unit
- `AGENTS.md` or `README.md` updated to reflect a real change in conventions or repo layout

Do NOT commit after minor typo fixes or cosmetic edits unless bundled with something meaningful.

---

## What to stage

```bash
git add scripts/ config/ AGENTS.md README.md pyproject.toml
```

`data/` and `outputs/` are gitignored and regenerable from a fresh clone via the scripts alone —
never force-add into them.

Check before committing:

```bash
git status          # what's changed
git diff --staged   # what's actually going in
```

---

## Commit message format

```
<stage-slug> — <one-line summary of what changed>

Optional: 2–3 bullet points if multiple files changed for different reasons.
```

**Examples:**

```
ingest-crm — add CRM export parser, handles the Q3 schema change

preprocessing-spend — fix currency conversion bug, was using stale FX rate

eda-v1 — first pass on channel decomposition, 3 channels flagged for exclusion

config — extend date range to include August, per scope update
```

Keep the slug short and dateable in hindsight. Avoid vague messages like "update" or "fix stuff".

---

## Useful commands

```bash
git log --oneline                       # pipeline diary at a glance
git diff <commit-hash> HEAD              # what changed since a milestone
git diff HEAD~1                          # what changed in the last commit
git show <commit-hash> -- scripts/preprocessing/spend.py   # one file at a specific point
```

---

## Suggested milestone tags (optional, for later)

If the pipeline reaches a clear milestone, tag it:

```bash
git tag ingest-complete
git tag eda-v1
git tag final-model
```

Tags make it easy to jump back to a specific pipeline state without remembering commit hashes.
