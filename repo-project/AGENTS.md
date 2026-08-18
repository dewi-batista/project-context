# AGENTS.md (template)

Instructions for coding agents (and human reviewers) working on this project. Copy into
`repo-<slug>/AGENTS.md` at project kickoff — after renaming `repo-project/` to `repo-<slug>/`, see
`context/misc/project-skeleton.md` §2 — and fill in the project-specific sections below (pipeline
order, repo layout, conventions) — the context-system sections are generic and should be kept
as-is.

---

## Getting context

`context/` (sibling to this repo, never git-tracked here) holds the project's durable history —
see `~/Documents/project-context/README.md` for the full pattern. Run the `get-context` skill
(`/get-context`) when asked to "get context" on the project, or at the start of a new session where
project history matters — it has the full reading order, kept there as the single source of truth
so it doesn't drift from this file.

Two directories are always off-limits to read directly, regardless of what `get-context` says:
`context/meetings/scratch/` (raw transcripts/gemini-summaries — read only as a documented fallback
when no reconciled summary exists yet) and `context/communication/scratch/` (untouched
communication originals, written once by the `summarise-communication` skill — never read back at
all, purely a human's future-reference archive).

## Logging new context

- **After each meeting**, run the `summarise-meeting` skill (`/summarise-meeting`) to reconcile the
  Gemini transcript + AI summary (read from `context/meetings/scratch/`) into
  `context/meetings/<name>.md`. Do this soon after the meeting, not batched up — reconciliation
  works best while the transcript is easy to spot-check and the decision log is still fresh context
  for catching discrepancies.
- **For everything else** — a pasted chat log, an email thread, a spoken conversation with no
  transcript — use the `summarise-communication` skill to extract decisions/action items and file
  them into the right place under `context/`.
- Both skills append an entry to `context/main-log.md` if it exists (an experimental chronological
  activity log — see that file's own header for what it's for).
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
