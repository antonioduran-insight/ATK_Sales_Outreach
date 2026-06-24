# ATK Sales Outreach

Antonio's fork of Frank's AI Token King LinkedIn outreach pipeline, adapted for a multi-market team spanning LATAM, Taiwan, and Vietnam.

---

## What this repo does

1. **Scrape** — Apify pulls LinkedIn leads matching target ICP (job title + geo filters)
2. **Score** — `linkedin_processor.py` runs an ICP model (industry, company size, AI/cloud keywords) and classifies leads HOT / WARM / COLD
3. **Generate** — ATK API (OpenAI-compatible, model `claude-sonnet-4.6`) writes personalised connection requests and value-touch messages per lead, injecting the sender's persona from `workshop_config.py`
4. **Export** — output CSV lands in `scripts/output/`, ready for CRM import or manual sending

---

## Daily outreach workflow

```
Apify scrape → ICP score → AI draft generation
       ↓
Claude-in-Chrome + LinkedIn Premium (manual review & send)
       ↓
Replies → Reply-Router agent →接球草稿
       ↓
Asana or CRM tracking → ALI-Auditor weekly audit
```

Detailed SOP: `sales/daily-ops-v2.md`

---

## Claude agents

Defined in `.claude/agents/` — invoke from Claude Code or Claude-in-Chrome.

| Agent | Role |
|---|---|
| `AirTalk-S1-BizDev` | Deep lead analysis + personalised 3-message sequence for each LinkedIn profile |
| `Reply-Router` | Classifies inbound replies, routes to right channel, generates a follow-up draft |
| `Kid-Sales` | Day-to-day sales execution — new client outreach, existing account management, collections |
| `ALI-Auditor` | 7-layer weekly audit: funnel integrity, reply authenticity scoring, realistic KPI adjustment |
| `Outreach-Sensei` | Senior strategy consultant — first-principles diagnosis, skill roadmap, tactic design review |

---

## Directory structure

```
scripts/            Python pipeline (scrape → score → draft → export)
  linkedin_processor.py   Core engine: ICP scoring, AI draft gen, reply analysis
  apify_linkedin_scraper.py
  s1_agent.py
  output/           Generated CSVs land here

strategies/         Four outreach playbooks (S1 direct / S2 intent / S5 community / S6 competitor)
sales/              Daily ops SOP, sales playbooks, CRM setup guides
.claude/agents/     Claude sub-agent definitions
lead-drafts/        Analysed lead files and draft batches
```

---

## Onboarding (workshop branch)

The `workshop/participant-setup` branch is a simplified version for team members joining the pipeline. It walks through:

- Python + dependency setup
- Filling in `.env` (ATK API key + Apify token)
- Editing `scripts/workshop_config.py` with their name, target titles, and geo filters
- Running `python run_pipeline.py` to produce their own output CSV

See `SETUP.md` on that branch for the written guide.

---

## Quick start (existing team)

```bash
cd scripts/
cp .env.example .env
# Fill in: ATK_API_KEY, ATK_API_BASE, ATK_MODEL, APIFY_TOKEN

pip install -r requirements.txt

python apify_linkedin_scraper.py        # scrape leads
python linkedin_processor.py score output/leads_xxx.csv
python linkedin_processor.py draft output/leads_xxx.csv --account kid
```
