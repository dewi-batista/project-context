# Project context system

A personal pattern for keeping durable, reusable project context — decisions, data quirks, meeting outcomes, comms — inside a structured `context/` directory in each project repo, instead of letting it live only in someone's memory, an inbox, or a one-off chat session.

This pattern grew organically on the Epidemic Sound MMM engagement (`~/Documents/cromen-wyllt/work/epidemic-sound/context/`). This repo extracts it into a standalone template + writeup so it's reusable on future projects, rather than something re-derived from scratch each time.

`context/` is one piece of a project — see [`context/misc/project-skeleton.md`](context/misc/project-skeleton.md)
for how it sits alongside `repo-project/` (renamed per-project — see below) and Claude Code's
directory access rules. That file is the setup checklist: read and work through it at project
kickoff, and fill in its per-project decisions (the repo rename, the access table) rather than just
skimming it once. (`misc/` lives inside `context/` — see the structure below.)

## Why

Two failure modes this solves:

1. **Context that only exists in a live conversation.** It dies the moment the session ends. Decisions made on a call, in a DM thread, in an email chain — none of it is durable unless someone deliberately writes it down somewhere findable.
2. **Context that's written down but scattered.** Transcripts in one place, notes in another, decisions nowhere in particular. A future reader — a teammate, or Claude in a fresh session with zero memory of the project — needs one obvious entry point, not a scavenger hunt.

## The structure

```
context/
├── main-summary.md           # the living project summary — Claude-maintained, not hand-written
├── main-log.md               # chronological activity log, one entry per logged event, appended
│                             # to automatically by the summarise-* skills — see the file's own
│                             # header
├── meetings/
│   ├── <name>.md             # reconciled, decision-focused summaries, directly in meetings/
│   │                         # (see "summarise-meeting" below) — the trustworthy version, and
│   │                         # the only thing here you normally need to read
│   └── scratch/
│       ├── transcripts/      # raw transcripts — ground truth, but messy
│       └── gemini-summaries/ # fast AI-generated summaries — lossy, sometimes overconfident paraphrasing
├── communication/
│   ├── emails/               # reformatted email threads
│   ├── direct-messages/      # reformatted chat/DM pastes
│   ├── offline/              # cleaned-up recaps of in-person/phone/unrecorded conversations
│   └── scratch/               # the untouched original of every email/DM/offline recap above, in
│       ├── emails/            # its original format, one-to-one by name. Claude never reads this
│       ├── direct-messages/   # back — it's a write-only archive kept purely for a human's future
│       └── offline/           # reference, not a working source (see summarise-communication skill).
└── misc/                     # non-context knowledge that isn't project decisions/comms — domain
                              # primers, running lessons-learned, per-project Claude operational
                              # notes, and everything below. See project-skeleton.md §3 for the
                              # access table (parts of misc/ are read-only, secrets are denied
                              # outright).
    ├── diagrams/             # Excalidraw/other diagram source files
    ├── important-documents/
    │   └── slides/           # decks, reference materials, pptx exports — finished deliverables,
    │                         # normally read-only or denied to Claude (see project-skeleton.md §3)
    └── notes/                # freeform per-topic deep dives — not scaffolded by default, add it
                              # here (not at the project root) once there's an actual need
```

`todo-project.md` (renamed `todo-<slug>.md` at kickoff, same treatment as `repo-project/` below) —
day-to-day working notes, agenda/prep for the next call, plus a running todo list at the bottom —
is the one non-code file that lives at the *project root*, not under `context/`, since it's the
file you actually write into most often. Everything else non-code is under `context/`;
`main-summary.md` stays Claude-maintained, not a scratchpad.

Deliberately minimal to start: `misc/notes/` (freeform topic notes) and `planning/` (checklists,
plan docs, still at the `context/` top level) aren't scaffolded by default — add them per-project
once there's an actual need, rather than carrying empty structure from day one. Same
`notes-<subject>.md` convention as before if/when you add `misc/notes/`; split into a subfolder per
workstream (e.g. `misc/notes/data/`, mirroring `repo-project/scripts/preprocessing/` on a
data-heavy project) if it grows past a handful of files.

`context/` is the whole non-code side of the project (`misc/`, meeting/communication
records) except `todo-project.md`, which sits at the project root alongside it. See
[`context/misc/project-skeleton.md`](context/misc/project-skeleton.md) §1 for why the project root
ends up being just `context/`, `todo-<slug>.md`, `repo-<slug>/`, and `.claude/`.

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

## How context gets in

Four intake paths — three backed by skills, one manual:

1. **A meeting with a transcript** → the `summarise-meeting` skill. Cross-checks the fast Gemini auto-summary against the full raw transcript, catches places where the paraphrase doesn't match what was actually said, writes a reconciled decision-focused summary directly to `meetings/<name>.md`, then propagates any confirmed decisions into `main-summary.md`'s Key Decisions table (and other sections they touch) so the living summary doesn't go stale.
2. **A chat log, an email thread, or a spoken conversation with no transcript at all** → the `summarise-communication` skill. First classifies which of the three it's looking at, saves the untouched original to `communication/scratch/`, then reformats/archives a cleaned-up (verbatim, not summarized) copy to the matching `communication/` subfolder — the skill itself carries the per-type formatting rules, so there's no separate style guide to consult. Then extracts decisions, action items, and open questions and saves those into whichever of the structures above already fits (or into `main-summary.md` directly for something that belongs at the top level, like a scope change).
3. **Any other new artifact relevant to the project** — a repo someone shares, a spec doc, a dataset description, a one-off link — that doesn't fit either shape above → the `update-project-context` skill. Actually looks at the artifact (e.g. `gh repo view`/clones a repo rather than guessing from the URL), checks it against what `main-summary.md` and `misc/notes/` already know, and routes only what's genuinely new to wherever the template already keeps that kind of thing (decisions → `main-summary.md`, a technical deep-dive → `misc/notes/`, something that changes the actual codebase → `AGENTS.md`).
4. **Manual notes** — written by hand into `todo-project.md` while working day-to-day, or into a `misc/notes/` directory (created when there's an actual need — see "The structure" above) for something that deserves its own topic file. Not skill-mediated, just following the same conventions so it stays discoverable later instead of living only in a scratch file that gets deleted.

## Conventions

- **`[[wikilink]]` cross-references.** `main-summary.md` links out to topic notes by filename stem (e.g. "see `[[notes-gqv]]`"), Obsidian-style — keeps notes individually readable but connected without duplicating content into `main-summary.md` itself.
- **Date-stamp and attribute every logged decision.** `**Redefined 2026-07-14 (per Dewi)**`, not just "we changed this." A decision without a date and an owner is unverifiable later, and worse, un-overridable — nobody can tell if it's still current.
- **Meeting filenames: `YYYYMMDD-attendee[-attendee2].md`.** Matched exactly across `meetings/scratch/transcripts/`, `meetings/scratch/gemini-summaries/`, and the reconciled `meetings/<name>.md`, so the three can be cross-referenced by name alone and a missing pairing is obvious at a glance. `communication/{emails,direct-messages,offline}/` and their `communication/scratch/` originals follow the same convention.
- **Reformat communication, don't paraphrase it.** Light cleanup only per source type — strip email signature blocks/quote-nesting, strip chat read-receipts/reaction noise, lightly clean up an offline recap — but preserve exact numbers, dates, and commitments verbatim. Handled by the `summarise-communication` skill, which carries the per-type rules directly rather than a separate style guide.
- **`communication/scratch/` is write-only.** It holds each source's untouched original, kept purely for a human's future reference in its original format — Claude never reads it back, full stop (unlike `meetings/scratch/`, which is a legitimate fallback when no reconciled summary exists yet; see `repo-project/AGENTS.md`).
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
```

**Then open `context/misc/project-skeleton.md` and work through it** — it's the setup checklist,
not just background reading: rename `repo-project/` to `repo-<slug>/` and `todo-project.md` to
`todo-<slug>.md` (and update the references that name still needs fixing in `project-skeleton.md`
itself and in `.claude/settings.json`), review the access table, and fill in
`context/main-summary.md`'s placeholders and `repo-<slug>/AGENTS.md`'s project-specific sections.
Then start logging as the project moves — don't wait until there's "enough" to write down.

## Related skills

- [`get-context`](~/.claude/skills/get-context/) — reads through `context/` in the right order and reports back a concise orientation; the single source of truth for that reading order, which `AGENTS.md` files point to rather than restate
- [`make-new-project`](~/.claude/skills/make-new-project/) — scaffolds a new project from this template in the current directory: copies, renames the placeholders to match the directory's name, git-inits the repo, and walks through `project-skeleton.md`'s kickoff checklist
- [`summarise-communication`](~/.claude/skills/summarise-communication/) — pasted chat, Gmail, or spoken-conversation recap → verbatim archive under `communication/` + structured context in the decision log
- [`summarise-meeting`](~/.claude/skills/summarise-meeting/) — transcript + AI summary → reconciled decision-focused summary, with confirmed decisions propagated into `main-summary.md`
- [`update-project-context`](~/.claude/skills/update-project-context/) — a new repo/doc/link relevant to the project → understood, checked against what's already known, and routed to whichever of `main-summary.md`/`misc/notes/`/`AGENTS.md` actually applies
