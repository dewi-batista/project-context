# Project workspace instructions

This workspace contains private project context in `context/`, Dewi's personal
`todo-project.md`, and deliverable code in `repo-project/`. Rename the TODO and
repository to match the project slug at kickoff and update these references.
Keep private context, client assets and credentials outside the code repository.
Read each repository's own `AGENTS.md` before changing its code. If there are
multiple repositories, record their purpose, remote and working branch here.

This repository itself is the reusable project-context template. When maintaining
this template, keep its project logs and document index free of real project history
and client-specific content. The project workflow below applies after scaffolding.

## Personal TODO

Dewi's `todo-project.md` is readable for context. Do not edit, overwrite, rename or
delete it unless Dewi explicitly requests that change. This applies to every tool,
including shell commands and scripts; never bypass a permission rule through
another tool. The one-time kickoff rename is part of scaffolding a new project.
Keep the `.claude/settings.json` TODO protection aligned with the renamed file.
Explicit authorization to edit the TODO does not itself remove a tool deny rule;
leave the rule intact and report any blocked requested edit.

## Completion and project logging

- Before the final response, append a dated entry to `context/main-log.md` for
  meaningful functionality changes, substantive fixes, architecture or scope
  decisions, releases, reusable test assets, and completed diagrams or demos.
- Read recent entries first. Record each event once with its outcome, relative
  links to implementation or artifacts, and relevant branch/commit, validation
  and deployment status. Distinguish verified results from proposals.
- Update `context/main-summary.md` only when current facts or decisions change.
- Skip routine questions, read-only checks, trivial cosmetic edits and iterations
  with no new durable outcome. Do not backfill unrelated history.
- Verify the written entry and its local links before reporting completion.
  A commit or an instructions update does not replace the project log.
- Keep secrets and raw private communication out of log entries. Follow the
  repository instructions for meeting intake and `context/meetings-log.md` upkeep.
- In a code-only clone without private context, report that logging is unavailable;
  do not fabricate a context tree.

## Folder conventions

- `context/documents/README.md` indexes canonical document locations. Link to
  shared originals when available; avoid maintaining duplicate local copies.
  State when a synced location writes back to the shared source.
- `context/documents/diagrams/`: keep editable `.excalidraw` or other source files;
  put matching PNG and SVG exports in `PNGs/` and `SVGs/` when needed.
- `context/documents/primers/`: reusable reference material. Preserve source
  attribution and avoid silently rewriting reference content during other work.
- `context/documents/slides/`: decks and finished presentations, unless a shared
  source is canonical and linked from the documents index instead.
- `context/misc/demos/YYYY-MM-DD-description/`: one folder per recording, keeping
  the exported video, editable project, source recording and narration together.
  Add a short README linking the finished artifact and editable source.
- `context/misc/notes/`: topic notes named `notes-<subject>.md`, or dated folders
  for investigations with supporting evidence. Split by workstream as needed.
- `context/misc/test-tools/`: private test generators, manifests and maintenance
  utilities. Keep expectations, source metadata and historical evidence in
  `fixtures/` when needed; link to canonical shared inputs rather than duplicating
  them. Document how to reproduce checks. Distinguish authored expectations and
  saved results from current verified behavior; flag missing ground truth.
- Create deeper folders only when needed. Never commit secrets, private client
  inputs or provider credentials to a deliverable repository.
