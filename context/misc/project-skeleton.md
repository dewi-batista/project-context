# Project skeleton

**Read and work through this at project kickoff, before creating anything else.** This is the
checklist for laying out a new project: copy the template (see `README.md`'s "Setting up a new
project"), then use §1–§3 below to make the per-project calls — rename `repo-project/`, review the
access table, confirm `context/misc/`'s files are actually filled in. This file lives at
`context/misc/project-skeleton.md` once copied, so it travels with the project as a record of what
was decided at setup, not just a one-time reference you consult and forget.

`context/` (see `README.md`) is one piece of a larger per-project layout. This doc covers the
rest: where `repo-project/` sits, Claude Code's directory access rules, and lessons that don't belong in
the context-system writeup specifically. Grew out of the Epidemic Sound MMM engagement; a starting
point to tweak per project, not a final answer.

---

## 1. Top-level skeleton

```
<project-name>/
  context/                    See README.md. Obsidian-only, never git-tracked (or tracked in its
                               own separate, unpublished history) — not part of repo-project/'s git repo.
    misc/
      primers/                 domain refreshers (stats/ML concepts, methodology explainers)
      lessons-learned.md        running list, updated as you go — not just at project close
      claude-setup.md           per-project Claude operational notes: credentials refs,
                               sheet IDs, auth method — never the credentials themselves
      project-skeleton.md       this file, once copied — the setup checklist for this project
    documents/
      slides/                  decks, reference materials, pptx exports
  next-meeting.md              day-to-day working notes: agenda/prep for the next call, plus a
                               running todo list at the bottom — the file you actually write into.
                               The one non-code file that lives at project root, not under context/,
                               since it's the thing you touch most often.
  repo-project/                RENAME to repo-<slug> at kickoff (e.g. repo-es) — the ONLY
                               git-tracked directory — see §2 (includes commit-guide.md, since
                               it's the only directory commits actually apply to). If you rename
                               it, update the repo-project/ paths in this file's own access table
                               (§3) and in .claude/settings.json to match.
  .claude/
    settings.json              project-level permission rules — see §3
```

Everything that isn't code lives under `context/`, with the single exception of `next-meeting.md` —
the project root ends up being `context/`, `next-meeting.md`, `repo-<slug>/`, and `.claude/`
(tooling config, not project knowledge).

**Why `context/` and `repo-project/` are strictly separate:** `context/` holds client-sensitive material
(transcripts, internal decisions, sometimes PII-adjacent notes) that should never end up on a
shared GitHub remote. `repo-project/` is the actual deliverable codebase with its own remote. On the ES
project this boundary was clean and worked well — the one thing to avoid is what happened there: a
second, vestigial `.git` repo got initialized at the project root (wrapping `repo-project/` as a
subdirectory) early on and then abandoned once `repo-project/`'s own git repo took over. It just sat there
accumulating a confusing diff against a structure that no longer existed. **Only git-init `repo-project/`,
never the project root.**

---

## 2. `repo-project/` skeleton (rename to `repo-<slug>/` at kickoff)

`repo-project/` is a placeholder name. Rename it to match the project — `repo-es/` for Epidemic
Sound, for example — as one of the first things you do, not something to circle back to later
(same reasoning as "settle folder names before the first working session" in §4). Once renamed,
update every `repo-project/` reference in this file's §1/§3 and in `.claude/settings.json` to the
new name.

This is the part you'll tweak most per project type — `ingest/` → `preprocessing/` → `eda/` is
MMM-specific. The scaffolding around it generalized well and is worth keeping regardless of
domain:

```
repo-project/
  AGENTS.md                   single source of truth for a coding agent: what the project is,
                               pipeline order, repo layout, and the conventions every script
                               follows. Template at repo-project/AGENTS.md — includes the
                               context-system workflow (summarise-meeting after every meeting,
                               add-project-context for everything else, read
                               context/main-summary.md + meetings/ for orientation)
                               plus placeholders for the project-specific sections.
  README.md                    one-page human-facing scope summary
  config/
    settings.yaml               single source of truth for parameters — date range, filters,
                               external table/API references, any taxonomy/grouping config.
                               Scripts read this, not hardcoded constants.
  scripts/
    ingest/                    one script per raw source → data/raw/*.csv
    preprocessing/              one script per source → data/pre-processed/*.csv
    eda/ (or analysis/)         exploratory work → outputs/**
    metadata.py                 shared write_metadata() sidecar helper — every output file gets
                               a matching metadata/*.meta.json (generating script, source files,
                               parameters, row count, a free-text "why")
    plotting.py                 shared chart styling, if the project produces charts
  data/                        gitignored: raw/, pre-processed/, scratch/
  outputs/                     gitignored: fully regenerable from data/ + scripts/
  pyproject.toml / uv.lock     dependency management
  .python-version
  .gitignore
```

Conventions worth carrying forward regardless of project type (all validated on ES) are listed in
`repo-project/AGENTS.md`.

---

## 3. Claude Code directory access control

Claude Code supports this natively via `permissions.allow` / `permissions.deny` / `permissions.ask`
in a `settings.json`, with per-tool, glob-scoped rules. Template at `.claude/settings.json`.

**Proposed default access table** (starting point — tweak per project):

| Directory | Access | Why |
|---|---|---|
| `repo-project/scripts/`, `repo-project/config/` | read-write | primary code Claude edits |
| `repo-project/data/`, `repo-project/outputs/` | read-write | pipeline needs full read/write; already gitignored |
| `context/**` | read-write | Claude actively builds/maintains meeting summaries + decision log |
| `context/misc/primers/`, `context/misc/lessons-learned.md` | read-only | reference material, shouldn't get silently rewritten |
| `context/misc/claude-setup.md` | read-write | Claude updates this as new access/credentials get granted |
| any secrets/credentials file | **deny (no access)** | see caveat below — don't rely on this alone |
| `context/documents/` | read-only, or deny | finished deliverables (slides, decks); rarely something Claude should edit |

**Caveats worth knowing (confirmed against current Claude Code docs), not just assumed:**

- **Deny on `Read` is the real mechanism for "no access"** — there's no separate `.claudeignore`. A
  `Read(path/**)` deny rule blocks Claude's Read/Grep/Glob tools *and* file-reading Bash commands it
  recognizes (`cat`, `head`, `sed`, etc.).
- **It is not airtight against arbitrary code Claude writes and runs.** If Claude writes a Python
  script and executes it via Bash, that subprocess can still open a denied file directly —
  permission rules gate Claude's own tool calls, not everything a subprocess can technically do.
  For anything that must be *truly* unreadable, pair this with Claude Code's
  `sandbox.filesystem.denyRead` (OS-level enforcement for Bash), not permissions alone.
- **Deny always wins over allow, across scope levels.** A project-level deny overrides a user-level
  allow for the same path, even if the user settings are "more permissive."
- **Officially, settings load from a project's own `.claude/settings.json` (or
  `.claude/settings.local.json`, gitignored/personal) or from `~/.claude/settings.json`
  (user-level, all projects)** — not from an arbitrary parent directory. A `work/.claude/settings.local.json`
  sitting one level above several project folders has appeared to apply across them in practice,
  which suggests Claude Code does walk upward from cwd looking for the nearest `.claude/` — but
  this isn't in the officially documented scope list, so treat it as observed-but-unconfirmed
  behavior rather than something to depend on long-term. Safer default: put project-specific rules
  in `<project-name>/.claude/settings.json`, and only put genuinely cross-project rules in
  `~/.claude/settings.json`.

**Biggest practical takeaway from this project: don't rely on Claude-side access rules as your only
safeguard for secrets.** The one real miss on ES was `misc/password.md` (pre-dates the current
`context/misc/` nesting, but the same risk applies wherever it lives) — a plaintext credentials
file sitting inside the same vault Claude has broad read access to. A `deny` rule fixes Claude
specifically, but the simplest actual fix is: **don't put secrets in the vault at all** — use a
password manager or `.env` file outside the tracked/visible tree, referenced by name only (as
`claude-setup.md` already does for the Google Sheets service account).

---

## 4. Other lessons carried over from ES, worth deciding on up front next time

- **Settle folder names before the first working session.** Mid-project renames (this happened 3
  times on ES) break wikilinks every time and require a manual sweep to fix. Cheap to avoid by
  deciding names once at kickoff — this is exactly what this repo is for.
- **Template the meetings pipeline (transcript → gemini-summary → reconciled summary) from day
  one**, not partway through. It was the single highest-value pattern this project produced — built
  as a proper Claude Skill (`summarise-meeting`) — but only after ~15 meetings had already
  accumulated as ad hoc notes on ES (in a `scratch-summaries/` folder since folded away — the
  current template skips straight to `meetings/<name>.md` once the skill exists from day one).
- **One notes file per source, one preprocessing script per source, one EDA/analysis section per
  source** — keeping these in 1:1:1 correspondence (same name, different folder) made
  cross-referencing trivial all project long.
- **`write_metadata()`-style sidecar files for every generated output** are worth setting up as
  infrastructure in week one, not retrofitted later once you have 50 ungoverned CSVs.

---

## Open ideas, not yet built

- Hook up to Gmail/GDrive/Slack by default for a new project, rather than per-project setup each
  time?
- A Claude skill that periodically sweeps `context/` and `repo-project/` to flag documentation that looks
  stale given more recent notes/decisions — a freshness check, not just a search.
