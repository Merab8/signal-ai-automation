# Signal — AI-Powered Lead Capture & CRM Automation

An end-to-end automation system that captures leads from a live landing page, validates and stores them in a CRM, qualifies them automatically, and triggers instant email responses — with zero manual work.

Built as part of my AI Automation Engineering internship at **Abdanix Solutions**, then extended into a full portfolio-grade product.

🔗 **Live demo:** [signal-agency-leads.netlify.app](https://signal-agency-leads.netlify.app)

---

## What it does

```
Visitor fills form on landing page
            ↓
   Webhook receives the data
            ↓
   Validates input (name/email present, email format correct)
            ↓
        ┌───┴───┐
     valid    invalid
        ↓         ↓
   Airtable    Clean error
   (CRM row     response
    created)    (no crash)
        ↓
   Rule-based lead scoring
   (Hot Lead / New Lead)
        ↓
   Airtable record updated
        ↓
   ┌────┴────┐
Welcome    Admin
 email    notification
```

- **Instant capture** — every submission is validated and stored the moment it happens
- **Zero missed leads** — invalid submissions get a clean error response instead of silently failing
- **Automatic follow-up** — the lead gets a personalized confirmation email within seconds
- **Smart tagging** — leads mentioning urgency ("urgent", "asap", "ready") are automatically flagged as Hot Leads
- **Admin alerting** — the business owner gets notified the moment a new lead comes in

---

## Tech Stack
<p> <img src="https://img.shields.io/badge/n8n-EA4B71?style=for-the-badge&logo=n8n&logoColor=white" alt="n8n"/> <img src="https://img.shields.io/badge/Airtable-18BFFF?style=for-the-badge&logo=airtable&logoColor=white" alt="Airtable"/> <img src="https://img.shields.io/badge/Gmail-EA4335?style=for-the-badge&logo=gmail&logoColor=white" alt="Gmail"/> <img src="https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white" alt="HTML5"/> <img src="https://img.shields.io/badge/CSS3-1572B6?style=for-the-badge&logo=css3&logoColor=white" alt="CSS3"/> <img src="https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black" alt="JavaScript"/> <img src="https://img.shields.io/badge/Netlify-00C7B7?style=for-the-badge&logo=netlify&logoColor=white" alt="Netlify"/> <img src="https://img.shields.io/badge/ngrok-1F1E37?style=for-the-badge&logo=ngrok&logoColor=white" alt="ngrok"/> <img src="https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white" alt="GitHub"/> </p>

| Layer | Tool | Why |
|---|---|---|
| Automation engine | [n8n](https://n8n.io) | Visual workflow orchestration, easy to extend |
| CRM / storage | Airtable | Structured lead data with tagging & views |
| Email | Gmail (SMTP) | Automated transactional emails |
| Frontend | HTML / CSS / vanilla JS | Lightweight, no framework overhead needed |
| Hosting | Netlify | Free static hosting for the landing page |
| Tunneling (dev) | ngrok | Exposes the local n8n instance publicly for the live demo |

---

## Project Structure

```
├── index.html          # Landing page + lead capture form
├── n8n-workflow.json   # Full exportable n8n workflow
└── screenshots/         # Workflow canvas + Airtable output
```

---

## How the workflow is built

1. **Webhook (Lead Form Input)** — receives POST requests from the landing page form
2. **IF (Validation)** — checks that the email field is present and contains `@`; invalid data is routed to a separate branch
3. **Airtable — Create Record** — stores Name, Email, Phone, Automation Need, Timestamp, Status
4. **Edit Fields — Calculate Lead Score** — a JavaScript expression scans the lead's message for urgency keywords and assigns a score
5. **Airtable — Update Record** — writes the score back into the same row
6. **Send Email (x2, parallel)** — a welcome email to the lead, and an internal alert to the admin
7. **Respond to Webhook** — returns a clean JSON error (`400`) if validation fails, instead of the workflow crashing

---

## Key Engineering Decisions

**Why Airtable over Google Sheets?**
Native support for tagging, single-select status fields, and a more CRM-like feel — while still being simple enough for a fast build.

**Why rule-based scoring instead of an LLM call?**
An LLM-based qualification step was tested (Gemini API) but dropped in favor of a deterministic rule-based check — more reliable for a production demo, no rate-limit risk, and instant response time. This is documented as a conscious engineering trade-off, not a limitation.

**Why explicit node referencing (`$('Node Name')`) instead of `$json`?**
In a multi-step n8n workflow, `$json` always refers to the *immediately preceding* node's output — not the original trigger data. Since the email step runs after Airtable (not directly after the webhook), the original lead data has to be referenced explicitly to avoid `undefined` values.

---

## Running it yourself

1. Import `n8n-workflow.json` into your own n8n instance
2. Reconnect the Airtable and SMTP credentials (not included in the export, for security)
3. Set the Airtable base/table IDs to your own base
4. Activate the workflow and copy its Production Webhook URL
5. Paste that URL into the `WEBHOOK_URL` constant in `index.html`
6. Host `index.html` anywhere (Netlify, Vercel, GitHub Pages)

---

## About this project

This was built during Week 4 of an AI Automation internship, then intentionally taken beyond the original assignment scope — real landing page, live public deployment, and lead qualification logic — to serve as a portfolio piece for freelance AI automation work.

**Built by Merab Butt** 
