# Project context system

A personal pattern for keeping durable, reusable project context — decisions, data quirks, meeting outcomes, comms — inside a structured `context/` directory in each project repo, instead of letting it live only in someone's memory, an inbox, or a one-off chat session.

This pattern grew organically on the Epidemic Sound MMM engagement (`~/Documents/cromen-wyllt/work/epidemic-sound/context/`). This repo extracts it into a standalone template + writeup so it's reusable on future projects, rather than something re-derived from scratch each time.

`context/` is one piece of a project — see [`context/misc/project-skeleton.md`](context/misc/project-skeleton.md)
for how it sits alongside `repo-project/` (renamed per-project — see below) and Claude Code's
directory access rules. That file is the setup checklist: read and work through it at project
kickoff, and fill in its per-project decisions (the repo rename, the access table) rather than just
skimming it once. (`misc/` lives inside `context/` — see the structure below.)

The workspace [AGENTS.md](AGENTS.md) owns personal TODO protection, completion logging and
folder conventions; `repo-project/AGENTS.md` holds repository-specific instructions.
`CLAUDE.md` imports the workspace instructions for Claude Code.

## Why

Two failure modes this solves:

1. **Context that only exists in a live conversation.** It dies the moment the session ends. Decisions made on a call, in a DM thread, in an email chain — none of it is durable unless someone deliberately writes it down somewhere findable.
2. **Context that's written down but scattered.** Transcripts in one place, notes in another, decisions nowhere in particular. A future reader — a teammate, or Claude in a fresh session with zero memory of the project — needs one obvious entry point, not a scavenger hunt.

## The structure

```
context/
├── main-summary.md           # the living project summary — Claude-maintained, not hand-written
├── main-log.md               # chronological activity log, one entry per logged event, appended
│                             # after meaningful work and communication intake; see AGENTS.md
├── meetings-log.md           # dated meeting tracker: schedule, status, outcomes and source links
├── communication/             # everything communicated, whether in a meeting or otherwise
│   ├── meetings/
│   │   └── <name>.md         # reconciled, decision-focused summaries, directly in meetings/
│   │                         # (see "update-project-context" below) — the trustworthy version, and
│   │                         # the only thing here you normally need to read
│   ├── emails/               # reformatted email threads
│   ├── direct-messages/      # reformatted chat/DM pastes
│   └── scratch/              # untouched communication originals
│       ├── meetings/         # fallback sources when no reconciled summary exists yet
│       │   ├── transcripts/      # raw transcripts — ground truth, but messy
│       │   └── gemini-summaries/ # fast AI-generated summaries — lossy, sometimes overconfident
│       ├── emails/           # email/DM originals are write-only archives for humans
│       └── direct-messages/
├── documents/               # reference documents and finished deliverables
│   ├── README.md            # index of canonical document locations and shared sources
│   ├── diagrams/            # Excalidraw/other diagram source files
│   ├── primers/             # domain refreshers and methodology explainers
│   └── slides/              # finished decks and pptx exports; normally read-only or denied
└── misc/
    ├── demos/               # demo assets and walkthroughs
    ├── notes/               # freeform per-topic deep dives
    ├── secrets/             # denied to Claude; never commit credentials
    ├── test-tools/          # reusable testing utilities and fixtures
    └── project-skeleton.md  # project setup checklist and access rules
```

`todo-project.md` (renamed `todo-<slug>.md` at kickoff, same treatment as `repo-project/` below) —
day-to-day working notes, agenda/prep for the next call, plus a running todo list at the bottom —
is the day-to-day notes file at the *project root*, alongside workspace instructions, since it's the
file you actually write into most often. Other project knowledge lives under `context/`;
`main-summary.md` stays Claude-maintained, not a scratchpad.

Reference documents live under `context/documents/`; add source-specific folders such as
`documents/google-drive/` when needed. Diagrams, primers and slides live in `documents/`. Demos, topic notes and test tools stay in `misc/`.

`misc/notes/` holds freeform topic notes. Use `notes-<subject>.md`; split into a subfolder per
workstream (e.g. `misc/notes/data/`, mirroring `repo-project/scripts/preprocessing/` on a
data-heavy project) if it grows past a handful of files. Add `planning/` at the `context/` top
level only when needed.

`context/` is the whole non-code side of the project (`documents/`, `misc/`, meeting/communication
records) except `todo-project.md`, which sits at the project root alongside it. See
[`context/misc/project-skeleton.md`](context/misc/project-skeleton.md) §1 for why the project root
contains `context/`, `todo-<slug>.md`, `repo-<slug>/`, `AGENTS.md`, `CLAUDE.md` and `.claude/`.

This repo's `context/` is exactly this skeleton, empty and ready to copy into a new project.

## `main-summary.md` — the entry point

The one file a future reader (human or Claude) should read first to get oriented. It's a *living* document — updated as the project progresses, not written once at kickoff and left stale — but Claude-maintained, not something you write into by hand day-to-day (that's `todo-project.md`'s job). Structure that's worked well:

- **What we're building** — scope, deliverables, what's explicitly out of scope
- **The client / team** — who's who, org, role
- **Timeline** — phases and key milestones
- **Data** — sources, granularity, known gaps
- **Technical setup** — where things live, how to access them
- **Quirks & things to know** — the "gotchas" that aren't obvious from the code/data itself (a filter bug that silently did nothing, a currency conversion that isn't what it looks like, etc.)
- **Key decisions logged** — a table: decision · detail · date · who decided it

See `context/main-summary.md` for a filled-in skeleton with prompts for each section.

## `meetings-log.md` — the meeting tracker

A chronological table of dated meetings: date, time, meeting, status, and a short outcome with
links to notes and scheduling evidence. Keep scheduled events, cancellations and reschedules;
RSVPs are separate from attendance, and outcomes need notes or confirmation. State the timezone,
when calendars were last checked and when meeting outcomes were last updated. Record recurring
cadence and open schedule questions below the table.

Update the matching row whenever meeting notes are processed, including an explicit label for
unverified source-only records. Detailed records stay in `communication/meetings/`, decisions in
`main-summary.md`, and completed work in `main-log.md`. See [the template](context/meetings-log.md)
for the columns and maintenance rules.

## How context gets in

Three source workflows in `update-project-context`, plus manual notes:

1. **A meeting with a transcript** → the `update-project-context` meeting workflow. Cross-checks the fast Gemini auto-summary against the full raw transcript, catches places where the paraphrase doesn't match what was actually said, writes a reconciled decision-focused summary directly to `communication/meetings/<name>.md`, then propagates any confirmed decisions into `main-summary.md`'s Key Decisions table (and other sections they touch) so the living summary doesn't go stale.
2. **A chat log, an email thread, or a spoken conversation with no transcript at all** → the `update-project-context` communication workflow. Saves email/chat originals to `communication/scratch/`, then reformats/archives a cleaned-up (verbatim, not summarized) copy to the matching `communication/` subfolder. Spoken recaps go into `communication/meetings/`, clearly labelled as recaps without transcript verification — the skill itself carries the per-type formatting rules, so there's no separate style guide to consult. Then extracts decisions, action items, and open questions and saves those into whichever of the structures above already fits (or into `main-summary.md` directly for something that belongs at the top level, like a scope change).
3. **Any other new artifact relevant to the project** — a repo someone shares, a spec doc, a dataset description, a one-off link — that doesn't fit either shape above → the `update-project-context` skill. Actually looks at the artifact (e.g. `gh repo view`/clones a repo rather than guessing from the URL), checks it against what `main-summary.md` and `misc/notes/` already know, and routes only what's genuinely new to wherever the template already keeps that kind of thing (decisions → `main-summary.md`, a technical deep-dive → `misc/notes/`, something that changes the actual codebase → `AGENTS.md`).
4. **Manual notes** — written by hand into `todo-project.md` while working day-to-day, or into the `misc/notes/` directory for something that deserves its own topic file. Not skill-mediated, just following the same conventions so it stays discoverable later instead of living only in a scratch file that gets deleted.

## Conventions

- **`[[wikilink]]` cross-references.** `main-summary.md` links out to topic notes by filename stem (e.g. "see `[[notes-gqv]]`"), Obsidian-style — keeps notes individually readable but connected without duplicating content into `main-summary.md` itself.
- **Date-stamp and attribute every logged decision.** `**Redefined 2026-07-14 (per Dewi)**`, not just "we changed this." A decision without a date and an owner is unverifiable later, and worse, un-overridable — nobody can tell if it's still current.
- **Meeting filenames: `YYYYMMDD-attendee[-attendee2].md`.** Matched exactly across `communication/scratch/meetings/transcripts/`, `communication/scratch/meetings/gemini-summaries/`, and the reconciled `communication/meetings/<name>.md`, so the three can be cross-referenced by name alone and a missing pairing is obvious at a glance. `communication/{emails,direct-messages}/` and their `communication/scratch/` originals follow the same convention.
- **Reformat communication, don't paraphrase it.** Light cleanup only per source type — strip email signature blocks/quote-nesting, strip chat read-receipts/reaction noise — but preserve exact numbers, dates, and commitments verbatim. Handled by the `update-project-context` communication workflow, which carries the per-type rules directly rather than a separate style guide.
- **`communication/scratch/` holds untouched originals.** Email and DM originals are write-only archives kept purely for a human's future reference — Claude never reads them back. `communication/scratch/meetings/` is the exception: it is a legitimate fallback when no reconciled summary exists yet (see `repo-project/AGENTS.md`).
- **Never commit raw client data or secrets.** A scratch/raw-data dump directory (e.g. `context/misc/`) stays gitignored. Treat the whole repo as if it could go public, even when it's private.

## Setting up a new project

Open a Claude Code session in the new (ideally empty) project directory and run
`/make-new-project` — no path or slug to type, the current directory and its name are both used
directly. It copies the template, renames the placeholders, fixes cross-references, git-inits the
repo, and walks through `project-skeleton.md`'s kickoff checklist with you (filling in
`main-summary.md` and `AGENTS.md`'s placeholders). See that skill for the exact steps.

Doing it by hand instead (what the skill automates):

```sh
cp -r ~/Documents/cromen-wyllt/work/project-context/context <path-to-new-project>/context
cp -r ~/Documents/cromen-wyllt/work/project-context/repo-project <path-to-new-project>/repo-project
cp ~/Documents/cromen-wyllt/work/project-context/todo-project.md <path-to-new-project>/todo-project.md
cp -r ~/Documents/cromen-wyllt/work/project-context/.claude <path-to-new-project>/.claude
cp ~/Documents/cromen-wyllt/work/project-context/AGENTS.md <path-to-new-project>/AGENTS.md
cp ~/Documents/cromen-wyllt/work/project-context/CLAUDE.md <path-to-new-project>/CLAUDE.md
```

**Then open `context/misc/project-skeleton.md` and work through it** — it's the setup checklist,
not just background reading: rename `repo-project/` to `repo-<slug>/` and `todo-project.md` to
`todo-<slug>.md` (and update the references that name still needs fixing in `project-skeleton.md`
itself and in `.claude/settings.json`), review the access table, and fill in
`context/main-summary.md`'s placeholders and `repo-<slug>/AGENTS.md`'s project-specific sections.
Ensure scaffolding copies both root instruction files and updates their renamed paths.
The `Edit(/todo-*.md)` deny rule keeps personal TODO files readable while blocking built-in
file modifications; instructions also prohibit shell/script workarounds. See
[Claude permission rules](https://code.claude.com/docs/en/permissions#read-and-edit) for coverage.
Then start logging as the project moves — don't wait until there's "enough" to write down.

## Related skills

- `get-project-context` — read-only orientation, run only when Dewi explicitly invokes
  `/get-project-context` (Claude) or `$get-project-context` (Codex).
- `make-new-project` — copy this template, rename placeholders, initialize the code
  repository and work through the kickoff checklist.
- `update-project-context` — source-specific workflows for meeting reconciliation,
  communication archival, and inspecting documents, repositories or links; updates
  durable context without duplication.
