# AGENTS.md (template)

Instructions for coding agents (and human reviewers) working on this project. Copy into
`repo-<slug>/AGENTS.md` at project kickoff — after renaming `repo-project/` to `repo-<slug>/`, see
`../context/misc/project-skeleton.md` §2 — and fill in the project-specific sections below (pipeline
order, repo layout, conventions) — the context-system sections are generic and should be kept
as-is.

---

## Getting context

Read the workspace [AGENTS.md](../AGENTS.md) for personal TODO protection,
completion logging and folder conventions. These apply alongside this repository's
instructions. Private project context is in `../context/`. Paths in this file are relative to
this repository; workspace instructions use paths relative to its parent.
Run `get-project-context` only when Dewi explicitly invokes `/get-project-context`
or `$get-project-context`; never invoke it automatically for orientation.
Use `../context/main-summary.md` for the current project summary and
`../context/misc/project-skeleton.md` for kickoff decisions.

Do not read `../context/communication/scratch/` during normal orientation, regardless of what
`get-project-context` says. Its `meetings/` subdirectory holds raw transcripts/gemini-summaries — read
only as a documented fallback when no reconciled summary exists yet. Archived spoken
recaps in `../context/communication/scratch/meetings/recaps/` remain write-only. Its email and DM
originals are written once by the `update-project-context` communication workflow and never read back at all;
they are purely a human's future-reference archive.

## Logging new context

- **After each meeting**, use `update-project-context`'s meeting workflow to reconcile
  transcript and Gemini sources in `../context/communication/scratch/meetings/`
  into `../context/communication/meetings/<name>.md`. Preserve source qualification
  and distinguish verified decisions from proposals.
- **For email, chat and spoken recaps**, use `update-project-context`'s communication
  workflow. Spoken recaps go in `../context/communication/meetings/`, labelled as
  recaps without transcript verification.
- **Keep `../context/meetings-log.md` current:** after processing a meeting, update its dated row
  with status, a short outcome and a link to notes. Follow the log's maintenance rules: separate
  RSVPs from attendance, preserve cancellations, qualify unverified records and record the
  timezone and source-check dates.
- `update-project-context` appends an entry to `../context/main-log.md` if it exists (a chronological activity log
  — see that file's own header for what it's for).
- **Decisions get a date and an owner** wherever they're logged: `**Redefined 2026-07-14 (per
  Dewi)**`, not just "we changed this." Undated, unattributed decisions are unverifiable later.

---

## What this project is

*(Fill in: one paragraph — domain, scope, key constraint, timeline.)*

## Setup

*(Fill in: environment setup commands — e.g. `uv sync`, auth steps.)*

## Pipeline, in order

*(Fill in: the actual script/stage order for this repo's `scripts/` — e.g. ingest →
preprocessing → analysis — with the run commands for each stage.)*

## Repo layout

*(Fill in: a short tree + one-line description per top-level folder in this repo.)*

## Conventions every script follows

*(Fill in: the load-bearing patterns specific to this codebase — path resolution, config loading,
metadata/logging conventions, anything a new contributor needs to match rather than reinvent.)*

Conventions worth carrying forward regardless of project type (validated on the Epidemic Sound MMM
engagement):

- Every output CSV is canonical; parquet is a dtype-preserving scratch backup, not the source of
  truth
- Assertions over silent coercion — data-quality invariants fail loudly, not silently
- Every imputed value gets an explicit `is_imputed` flag column, never blended in invisibly
- Inline comments distinguish **working assumption** from **CONFIRMED** (with date + who), and get
  updated in place when a question resolves — don't just delete the caveat
- `data/` and `outputs/` gitignored and regenerable from a fresh clone via the scripts alone
