# Case study 01 — AI Lead Qualifier

> Status: **in progress**. Fill in the numbers as you build.

## Why this project first

It stacks the four things Upwork clients pay most for into a single build:

1. **Webhook intake** — the backbone of every "connect my form to my CRM" job.
2. **AI/LLM node** — the 2–3x price premium.
3. **CRM write** — the highest-repeat-business category.
4. **Error handling** — what separates a $500 freelancer from a $2,500 one.

Ship this and you can credibly bid on the $400–$2,500 tier of jobs.

## The scenario (hypothetical business, real workflow)

A 6-person commercial roofing company gets ~40 inbound leads/day from their website
form. An office manager reads each one, decides if it's worth a callback, types it into
a spreadsheet, and emails the right estimator. That's ~2.5 hours/day of a $22/hr
employee — roughly **$14,000/year** of salary spent on copy-paste.

## What we build

```
Webhook (form submission)
   ↓
Normalize / validate fields          ← Set + IF nodes
   ↓
AI: score the lead 1–10, classify    ← AI node (Google Gemini)
    urgency, draft a reply email
   ↓
IF score >= 7 ─── yes ──→ Sheet row + email estimator + "hot lead" flag
              └── no  ──→ Sheet row only + auto-nurture reply
   ↓
Error Trigger workflow → alerts you when anything breaks
```

## Deliverables for the portfolio

- [ ] Working workflow, exported to `workflows/01-ai-lead-qualifier.json`
- [ ] A 90-second Loom of a test lead flowing through end to end
- [ ] This file, completed with real before/after numbers
- [ ] Screenshot of the canvas (clean, labelled nodes — clients judge this)

## Result (fill in when done)

- Time to process one lead: **before** ~4 min manual → **after** ___ seconds
- Hours saved per week: ___
- Estimated annual saving: ___
