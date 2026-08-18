# Project context system

A personal pattern for keeping durable, reusable project context — decisions, data quirks, meeting outcomes, comms — inside a structured `context/` directory in each project repo, instead of letting it live only in someone's memory, an inbox, or a one-off chat session.

This pattern grew organically on the Epidemic Sound MMM engagement (`~/Documents/cromen-wyllt/work/epidemic-sound/context/`). This repo extracts it into a standalone template + writeup so it's reusable on future projects, rather than something re-derived from scratch each time.

`context/` is one piece of a project — see [`project-skeleton.md`](project-skeleton.md) for how it
sits alongside `repo/`, `misc/`, and Claude Code's directory access rules.

## Why

Two failure modes this solves:

1. **Context that only exists in a live conversation.** It dies the moment the session ends. Decisions made on a call, in a DM thread, in an email chain — none of it is durable unless someone deliberately writes it down somewhere findable.
2. **Context that's written down but scattered.** Transcripts in one place, notes in another, decisions nowhere in particular. A future reader — a teammate, or Claude in a fresh session with zero memory of the project — needs one obvious entry point, not a scavenger hunt.

## The structure

```
context/
├── summaries/
│   └── main.md              # the living project summary — single source of truth
├── meetings/
│   ├── transcripts/         # raw transcripts — ground truth, but messy
│   ├── gemini-summaries/    # fast AI-generated summaries — lossy, sometimes overconfident paraphrasing
│   ├── claude-summaries/    # reconciled, decision-focused summaries (see "summarise-meeting" below)
│   └── scratch-summaries/   # rough/early drafts not yet reconciled
├── communication/
│   ├── emails/               # reformatted email threads (see email-formatting-guide.md)
│   ├── direct-messages/      # chat/DM pastes
│   └── email-formatting-guide.md
├── planning/                 # checklists, plan docs, one-off scripts (e.g. deck builders)
└── notes/                    # freeform topic notes — notes-<subject>.md, one per subject.
                               # On a data-heavy project, split into notes/data/ to mirror
                               # repo/scripts/preprocessing/ one deep-dive note per source
                               # (methodology, open questions, assumptions). More generally,
                               # split into per-workstream folders (e.g. modelling/) — same
                               # notes-<subject>.md convention either way.
```

This repo's `context/` is exactly this skeleton, empty and ready to copy into a new project.

## `summaries/main.md` — the entry point

The one file a future reader (human or Claude) should read first to get oriented. It's a *living* document — updated as the project progresses, not written once at kickoff and left stale. Structure that's worked well:

- **What we're building** — scope, deliverables, what's explicitly out of scope
- **The client / team** — who's who, org, role
- **Timeline** — phases and key milestones
- **Data** — sources, granularity, known gaps
- **Technical setup** — where things live, how to access them
- **Quirks & things to know** — the "gotchas" that aren't obvious from the code/data itself (a filter bug that silently did nothing, a currency conversion that isn't what it looks like, etc.)
- **Key decisions logged** — a table: decision · detail · date · who decided it

See `context/summaries/main.md` for a filled-in skeleton with prompts for each section.

## How context gets in

Three intake paths — two backed by skills, one manual:

1. **A meeting with a transcript** → the `summarise-meeting` skill. Cross-checks the fast Gemini auto-summary against the full raw transcript, catches places where the paraphrase doesn't match what was actually said, writes a reconciled decision-focused summary to `meetings/claude-summaries/`.
2. **A chat log, an email thread, or a spoken conversation with no transcript at all** → the `add-project-context` skill. Extracts decisions, action items, and open questions from a pasted Google Chat log, a fetched Gmail thread, or your own recap of a conversation that was never recorded — and saves it into whichever of the structures above already fits (or into `summaries/main.md` directly for something that belongs at the top level, like a scope change).
3. **Manual notes** — written by hand into `notes/` (or a workstream subfolder) while working something out. Not skill-mediated, just following the same structure so it stays discoverable later instead of living only in a scratch file that gets deleted.

## Conventions

- **`[[wikilink]]` cross-references.** `main.md` links out to topic notes by filename stem (e.g. "see `[[notes-gqv]]`"), Obsidian-style — keeps notes individually readable but connected without duplicating content into `main.md` itself.
- **Date-stamp and attribute every logged decision.** `**Redefined 2026-07-14 (per Dewi)**`, not just "we changed this." A decision without a date and an owner is unverifiable later, and worse, un-overridable — nobody can tell if it's still current.
- **Meeting filenames: `YYYYMMDD-attendee[-attendee2].md`.** Matched exactly across `transcripts/`, `gemini-summaries/`, and `claude-summaries/`, so the three can be cross-referenced by name alone and a missing pairing is obvious at a glance.
- **Reformat emails, don't paraphrase them.** Light cleanup only — strip signature blocks and quote-nesting, reorder chronologically (oldest first) — but preserve exact numbers, dates, and commitments verbatim. See `context/communication/email-formatting-guide.md`.
- **Never commit raw client data or secrets.** A scratch/raw-data dump directory (e.g. `misc/`) stays gitignored. Treat the whole repo as if it could go public, even when it's private.

## Setting up a new project

```sh
cp -r ~/Documents/project-context/context <path-to-new-project>/context
cp -r ~/Documents/project-context/repo <path-to-new-project>/repo
cp -r ~/Documents/project-context/.claude <path-to-new-project>/.claude
cp -r ~/Documents/project-context/misc <path-to-new-project>/misc
```

Then fill in `context/summaries/main.md`'s placeholders and `repo/AGENTS.md`'s project-specific
sections, and start logging as the project moves — don't wait until there's "enough" to write down.
See [`project-skeleton.md`](project-skeleton.md) for what `repo/`, `misc/`, and `.claude/settings.json`
are for.

## Related skills

- [`add-project-context`](~/.claude/skills/add-project-context/) — pasted chat, Gmail, or spoken-conversation recap → structured context
- [`summarise-meeting`](~/.claude/skills/summarise-meeting/) — transcript + AI summary → reconciled decision-focused summary
