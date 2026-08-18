# AGENTS.md (template)

Instructions for coding agents (and human reviewers) working on this project. Copy into
`repo/AGENTS.md` at project kickoff and fill in the project-specific sections below (pipeline
order, repo layout, conventions) — the context-system sections are generic and should be kept
as-is.

---

## Getting context

`context/` (sibling to this repo, never git-tracked here) holds the project's durable history —
see `~/Documents/project-context/README.md` for the full pattern. When asked to "get context" on
the project, or at the start of a new session where project history matters, read in this order:

1. `context/summaries/main.md` — the living project summary: scope, team, timeline, data
   inventory, quirks, and a running "Key Decisions" table of what's confirmed vs. still open. The
   single fastest way to get a new session oriented.
2. `context/meetings/<name>.md` — **not** `meetings/scratch/gemini-summaries/` or
   `meetings/scratch/transcripts/` directly. The reconciled files sitting directly in `meetings/`
   are the fact-checked record; reading Gemini's raw summary instead means re-trusting something
   already known to contain paraphrase errors, and reading raw transcripts means redoing work
   that's already been done.
3. `context/notes/` — per-topic deep dives (methodology, open questions, assumptions). On a
   data-heavy project these live in `context/notes/data/` and mirror `repo/scripts/preprocessing/`
   one-to-one by source name.
4. Only fall back to a meeting's raw transcript/gemini-summary in `meetings/scratch/` if no
   reconciled `meetings/<name>.md` exists yet (i.e. it's still pending reconciliation) — check the
   discrepancies section of the newest related reconciled summaries first, since they often surface
   open items relevant to whatever you're being asked about.

Don't reconstruct project history from raw transcripts when a reconciled `meetings/<name>.md`
already exists for that meeting — it's slower, and you'd be re-deriving something already
verified.

## Logging new context

- **After each meeting**, run the `summarise-meeting` skill (`/summarise-meeting`) to reconcile the
  Gemini transcript + AI summary (read from `context/meetings/scratch/`) into
  `context/meetings/<name>.md`. Do this soon after the meeting, not batched up — reconciliation
  works best while the transcript is easy to spot-check and the decision log is still fresh context
  for catching discrepancies.
- **For everything else** — a pasted chat log, an email thread, a spoken conversation with no
  transcript — use the `add-project-context` skill to extract decisions/action items and file them
  into the right place under `context/`.
- **Decisions get a date and an owner** wherever they're logged: `**Redefined 2026-07-14 (per
  Dewi)**`, not just "we changed this." Undated, unattributed decisions are unverifiable later.

---

## What this project is

*(Fill in: one paragraph — domain, scope, key constraint, timeline.)*

## Setup

*(Fill in: environment setup commands — e.g. `uv sync`, auth steps.)*

## Pipeline, in order

*(Fill in: the actual script/stage order for this project's `repo/scripts/` — e.g. ingest →
preprocessing → analysis — with the run commands for each stage.)*

## Repo layout

*(Fill in: a short tree + one-line description per top-level `repo/` folder.)*

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
