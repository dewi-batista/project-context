# Project context system

A personal pattern for keeping durable, reusable project context — decisions, data quirks, meeting outcomes, comms — inside a structured `context/` directory in each project repo, instead of letting it live only in someone's memory, an inbox, or a one-off chat session.

This pattern grew organically on the Epidemic Sound MMM engagement (`~/Documents/cromen-wyllt/work/epidemic-sound/context/`). This repo extracts it into a standalone template + writeup so it's reusable on future projects, rather than something re-derived from scratch each time.

`context/` is one piece of a project — see [`project-skeleton.md`](project-skeleton.md) for how it
sits alongside `repo/` and Claude Code's directory access rules. (`misc/` lives inside `context/`
— see the structure below.)

## Why

Two failure modes this solves:

1. **Context that only exists in a live conversation.** It dies the moment the session ends. Decisions made on a call, in a DM thread, in an email chain — none of it is durable unless someone deliberately writes it down somewhere findable.
2. **Context that's written down but scattered.** Transcripts in one place, notes in another, decisions nowhere in particular. A future reader — a teammate, or Claude in a fresh session with zero memory of the project — needs one obvious entry point, not a scavenger hunt.

## The structure

```
context/
├── main-summary.md           # the living project summary — Claude-maintained, not hand-written
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
│   └── offline/              # cleaned-up recaps of in-person/phone/unrecorded conversations
├── misc/                     # non-context knowledge that isn't project decisions/comms —
│                             # domain primers, running lessons-learned, per-project Claude
│                             # operational notes. See project-skeleton.md §3 for the access
│                             # table (parts of misc/ are read-only, secrets are denied outright).
├── documents/
│   └── slides/               # decks, reference materials, pptx exports — finished deliverables,
│                             # normally read-only or denied to Claude (see project-skeleton.md §3)
└── next-meeting.md           # day-to-day working notes — agenda/prep for the next call, plus a
                              # running todo list at the bottom. This is the file you actually
                              # write into day-to-day; main-summary.md is Claude-maintained.
```

Deliberately minimal to start: `notes/` (freeform topic notes) and `planning/` (checklists, plan
docs) aren't scaffolded by default — add them per-project once there's an actual need, rather than
carrying empty structure from day one. Same `notes-<subject>.md` convention as before if/when you
add `notes/`; split into a subfolder per workstream (e.g. `notes/data/`, mirroring
`repo/scripts/preprocessing/` on a data-heavy project) if it grows past a handful of files.

`context/` is the whole non-code side of the project — everything that isn't `repo/` lives here,
including `misc/`, `documents/`, and `next-meeting.md`. See [`project-skeleton.md`](project-skeleton.md)
§1 for why the project root ends up being just `context/`, `repo/`, and `.claude/`.

This repo's `context/` is exactly this skeleton, empty and ready to copy into a new project.

## `main-summary.md` — the entry point

The one file a future reader (human or Claude) should read first to get oriented. It's a *living* document — updated as the project progresses, not written once at kickoff and left stale — but Claude-maintained, not something you write into by hand day-to-day (that's `next-meeting.md`'s job). Structure that's worked well:

- **What we're building** — scope, deliverables, what's explicitly out of scope
- **The client / team** — who's who, org, role
- **Timeline** — phases and key milestones
- **Data** — sources, granularity, known gaps
- **Technical setup** — where things live, how to access them
- **Quirks & things to know** — the "gotchas" that aren't obvious from the code/data itself (a filter bug that silently did nothing, a currency conversion that isn't what it looks like, etc.)
- **Key decisions logged** — a table: decision · detail · date · who decided it

See `context/main-summary.md` for a filled-in skeleton with prompts for each section.

## How context gets in

Three intake paths — two backed by skills, one manual:

1. **A meeting with a transcript** → the `summarise-meeting` skill. Cross-checks the fast Gemini auto-summary against the full raw transcript, catches places where the paraphrase doesn't match what was actually said, writes a reconciled decision-focused summary directly to `meetings/<name>.md`.
2. **A chat log, an email thread, or a spoken conversation with no transcript at all** → the `add-project-context` skill. First classifies which of the three it's looking at and reformats/archives it (verbatim, cleaned up) to the matching `communication/` subfolder — the skill itself carries the per-type formatting rules, so there's no separate style guide to consult. Then extracts decisions, action items, and open questions and saves those into whichever of the structures above already fits (or into `main-summary.md` directly for something that belongs at the top level, like a scope change).
3. **Manual notes** — written by hand into `next-meeting.md` while working day-to-day, or into a `notes/` directory (created when there's an actual need — see "The structure" above) for something that deserves its own topic file. Not skill-mediated, just following the same conventions so it stays discoverable later instead of living only in a scratch file that gets deleted.

## Conventions

- **`[[wikilink]]` cross-references.** `main-summary.md` links out to topic notes by filename stem (e.g. "see `[[notes-gqv]]`"), Obsidian-style — keeps notes individually readable but connected without duplicating content into `main-summary.md` itself.
- **Date-stamp and attribute every logged decision.** `**Redefined 2026-07-14 (per Dewi)**`, not just "we changed this." A decision without a date and an owner is unverifiable later, and worse, un-overridable — nobody can tell if it's still current.
- **Meeting filenames: `YYYYMMDD-attendee[-attendee2].md`.** Matched exactly across `meetings/scratch/transcripts/`, `meetings/scratch/gemini-summaries/`, and the reconciled `meetings/<name>.md`, so the three can be cross-referenced by name alone and a missing pairing is obvious at a glance. `communication/{emails,direct-messages,offline}/` follow the same convention.
- **Reformat communication, don't paraphrase it.** Light cleanup only per source type — strip email signature blocks/quote-nesting, strip chat read-receipts/reaction noise, lightly clean up an offline recap — but preserve exact numbers, dates, and commitments verbatim. Handled by the `add-project-context` skill, which carries the per-type rules directly rather than a separate style guide.
- **Never commit raw client data or secrets.** A scratch/raw-data dump directory (e.g. `context/misc/`) stays gitignored. Treat the whole repo as if it could go public, even when it's private.

## Setting up a new project

```sh
cp -r ~/Documents/project-context/context <path-to-new-project>/context
cp -r ~/Documents/project-context/repo <path-to-new-project>/repo
cp -r ~/Documents/project-context/.claude <path-to-new-project>/.claude
```

Then fill in `context/main-summary.md`'s placeholders and `repo/AGENTS.md`'s project-specific
sections, and start logging as the project moves — don't wait until there's "enough" to write down.
See [`project-skeleton.md`](project-skeleton.md) for what `repo/`, `context/misc/`, and
`.claude/settings.json` are for.

## Related skills

- [`add-project-context`](~/.claude/skills/add-project-context/) — pasted chat, Gmail, or spoken-conversation recap → verbatim archive under `communication/` + structured context in the decision log
- [`summarise-meeting`](~/.claude/skills/summarise-meeting/) — transcript + AI summary → reconciled decision-focused summary
