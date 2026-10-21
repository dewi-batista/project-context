# [Project Name] — Meetings log

One row per dated meeting, in chronological order, linking to the source and meeting notes.
Calendar invitations and RSVPs describe scheduling, not proof that a meeting happened or that
someone attended. Record outcomes only when supported by notes or explicit confirmation.

**Timezone:** [IANA timezone, e.g. Europe/London; state exceptions per row]
**Calendar last checked:** [Not checked yet, or date/time, calendar/account and date range checked]
**Outcomes last updated:** [Not updated yet, or date and latest meeting/source reviewed]

| Date | Time | Meeting | Status | Notes / source |
|---|---|---|---|---|

## Recurring schedule and checks

[Add verified recurring times and timezone, series end dates, exceptions and open questions.
Recheck upcoming invitations before relying on an older snapshot.]

## Sources and maintenance

- Keep one row per dated meeting, including cancellations and rescheduled occurrences. Identify
  replacements so they are not mistaken for two held meetings.
- Distinguish scheduled, cancelled and confirmed-held meetings from RSVP and attendance. Leave
  unknown times, attendance and outcomes explicitly unknown.
- After processing a meeting, update its row with a short outcome and a relative link to notes in
  `communication/meetings/`. Label Gemini-only or otherwise unverified records explicitly.
- When checking calendars, record the account/calendar, search period, check time and coverage
  limits; link to invitation evidence when available. Do not copy conference access details or
  unrelated private events into this log.
- Extend future rows when the schedule is reviewed. Preserve historical cancellations and
  qualify stale invitations rather than inferring that past scheduled meetings took place.
- Detailed meeting records stay in `communication/meetings/`, project decisions in
  [main-summary.md](main-summary.md), and completed work in [main-log.md](main-log.md).
