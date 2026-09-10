# AI Lead Qualifier — n8n Automation

**An automation that reads every inbound lead, scores it with AI, routes the hot ones to a
human within seconds, and never silently fails.**

Built with n8n + Google Gemini. Replaces roughly 2.5 hours/day of manual lead triage.

![n8n](https://img.shields.io/badge/n8n-workflow%20automation-EA4B71)
![Gemini](https://img.shields.io/badge/AI-Google%20Gemini-4285F4)
![Status](https://img.shields.io/badge/status-in%20development-yellow)

> 🚧 **Status:** build in progress. The architecture and case study below are final;
> the exported workflow JSON, canvas screenshot and demo video land in this repo as
> each piece is finished. Watch the repo if you want the finished version.

---

## The problem this solves

A small commercial roofing company takes ~40 inbound leads a day through its website form.
An office manager opens each one, decides whether it's worth a callback, retypes it into a
spreadsheet, and emails the right estimator.

That is about **2.5 hours a day** of a $22/hr employee doing copy-paste — roughly
**$14,000 a year** — and it means a genuinely urgent lead can sit unread until the
afternoon.

The scenario is representative rather than a specific client; the workflow is real and runs.

## What it does

```
Webhook — website form submission
   │
   ▼
Normalize + validate fields                    Set / IF nodes
   │                                           (drops junk, standardizes phone,
   │                                            email, postcode, job type)
   ▼
AI scoring                                     Google Gemini
   • rates the lead 1–10
   • classifies urgency
   • drafts a tailored reply email
   │
   ▼
IF score >= 7
   ├── HOT  ──▶ spreadsheet row  +  email the right estimator  +  hot-lead flag
   └── COLD ──▶ spreadsheet row  +  automated nurture reply
   │
   ▼
Error Trigger workflow — alerts a human the moment anything breaks
```

## Why it's built this way

| Design decision | Reason |
|---|---|
| Validate *before* the AI call | Junk submissions never burn an API call against the rate limit, and the model gets clean input |
| AI returns structured output, not prose | The score is a number the workflow can branch on — not something a human has to read |
| Threshold routing (`score >= 7`) | The client tunes one number to change how aggressive triage is. No rebuild needed |
| Every lead is logged, hot or cold | Nothing disappears. Cold leads are still a nurture list |
| Dedicated Error Trigger workflow | A silent automation failure costs more than no automation. Breakage alerts a human |
| Credentials in n8n's credential store | No API keys in the workflow JSON, so this repo is safe to publish |

That error-handling layer is the part most quick builds skip, and it's the difference
between an automation a business can actually depend on and one that quietly stops
working in month two.

## Tech stack

- **n8n** — self-hosted workflow orchestration
- **Google Gemini API** (2.5 Flash, via n8n's Google Gemini Chat Model node) — lead scoring,
  urgency classification, reply drafting
- **Webhooks** — form intake from any website or form provider
- **Google Sheets / CRM write** — swappable for HubSpot, Pipedrive, Airtable
- **SMTP / email node** — estimator notification and nurture replies

## Results

| Metric | Before | After |
|---|---|---|
| Time to process one lead | ~4 minutes, manual | _measuring_ |
| Time until a hot lead reaches an estimator | up to a working day | _measuring_ |
| Hours of manual triage per week | ~12.5 | _measuring_ |
| Estimated annual saving | — | _measuring_ |

Numbers get filled in from real execution data, not estimates.

## Repository structure

```
├── workflows/          Exported n8n workflow JSON — importable, reviewable
├── case-studies/       Written case study: problem, build, measured outcome
├── notes/              Working notes on the n8n concepts behind the build
└── .env.example        Required environment variables (no secrets committed)
```

## Run it yourself

```bash
npx n8n start            # opens http://localhost:5678
```

Then:

1. Import `workflows/01-ai-lead-qualifier.json` from the n8n canvas
2. Copy `.env.example` to `.env` and add your own `GEMINI_API_KEY` (free key at [aistudio.google.com/apikey](https://aistudio.google.com/apikey))
3. Add your Google Sheets and SMTP credentials in n8n's credential store
4. Hit the webhook test URL with a sample form payload

No real credentials are stored in this repository — `.env` and n8n's local database are
gitignored.

---

## Work with me

I build automations like this one: intake → AI decision → system of record → human hand-off,
with the error handling that keeps it running after I'm gone.

**Things I'm a good fit for**

- AI and LLM integrations — scoring, classification, extraction, drafting, routing
- Connecting a website form, CRM, spreadsheet and inbox into one flow
- Zapier / Make → n8n migrations, usually to cut per-task cost and unlock custom logic
- Custom API integrations against services with no off-the-shelf node
- Rescuing an automation someone else built that's now failing or unmaintained

**Contact** — [github.com/mehrsaref](https://github.com/mehrsaref) · open an issue on this
repo, or reach me at the email on my GitHub profile.
