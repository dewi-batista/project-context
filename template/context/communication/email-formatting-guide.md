# Email formatting guide

Use this when reformatting copy-pasted email threads for use as project context.

---

## The problem with raw copy-pasted emails

- Excessive blank lines from HTML line-height rendering
- Deeply nested quote indentation (`> > > > >`) that makes threads hard to scan
- Email signature blocks (tables, logos, legal disclaimers, contact info) that add noise with zero signal
- Mailto links and @-mentions that don't render usefully in markdown
- Chronological order is reversed (newest first) — the opposite of how you want to read context

---

## Reformatting method

**1. Add a header block**
At the top of the file, include:
- Subject line or descriptive title
- Date range of the thread
- List of participants and their organisations

```
# Email chain — [subject]
**Period:** [date range] · **Participants:** Name (Org), Name (Org)
```

For a single email:
```
# Email — [subject]
**Date:** [date] · **From:** Name (Org) · **To:** [recipients]
```

**2. Order chronologically (oldest first)**
Reverse the default email thread order so the conversation reads naturally top-to-bottom.

**3. Use a flat header per email — no nesting**
Replace `> > > >` quote nesting with a simple heading:
```
### Sender → recipient(s) · Date
```

**4. Strip entirely:**
- Email signature blocks (name, title, phone, address, logo, legal footer)
- Blank lines created by HTML rendering (keep one blank line between paragraphs, none within them)
- Repeated sign-offs ("Best regards", "Kind regards", etc.)
- Mailto links — just use the person's name in plain text
- "On [date] [person] wrote:" transition lines — replaced by the flat header

**5. Preserve without paraphrasing:**
- All decisions, commitments, and action items
- Bullet point lists (clean them up but don't summarise them away)
- Any specific numbers, dates, or named systems
- Questions that were asked (even if answered later in the thread)

**6. Light condensing is fine, heavy summarising is not**
The goal is readable context, not a summary. A future reader (human or LLM) should be able to reconstruct what was said from the reformatted file. If in doubt, keep the content.

---

## What the output looks like

```markdown
# Email chain — Data requirements | [Project]
**Period:** 10–15 Jun 2026 · **Participants:** A (Org 1), B (Org 2)

---

### A → B · 10 Jun 2026

Following up on our recent call — sharing the data requirements for the project...

---

### B → A & team · 10 Jun 2026

Thanks for sending the templates over...
```

---

## When to use a summary .md instead

If the email thread is purely logistical (scheduling, access confirmations, "see you tomorrow") with no substantive content, don't reformat it — write a one-paragraph summary `.md` instead and note it's a distilled version of an email exchange.
