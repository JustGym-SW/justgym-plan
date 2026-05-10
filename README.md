# JustGym — Automation & Growth Plan

A multi-page internal plan covering Gmail migration, inbound email automation, and outbound B2B outreach — hosted on GitHub Pages.

**Live site:** https://justgym-sw.github.io/justgym-plan/

---

## Pages

| Page | Description |
|---|---|
| [Overview](index.html) | Summary of the full plan, key rules, and how the two automations fit together |
| [Moving to Gmail](gmail.html) | Six-step guide to setting up Google Workspace, creating two inboxes, migrating email, and connecting to Zapier |
| [Inbound Email Strategy](inbound.html) | Hourly automation: Knowledge Base search, auto-reply or WhatsApp escalation, sleeper member rules, logging |
| [Outbound B2B Campaign](outbound.html) | Daily automation: SerpAPI → Hunter → Claude → Gmail, with target profile, safeguards, and email rules |
| [Tech Stack](stack.html) | All tools, Zapier App IDs, costs, and step-by-step authentication instructions |

---

## Automations

### Inbound Triage
- **Trigger:** Every hour
- **Inbox:** support@justgym.co.uk
- **Flow:** Fetch unread → check sleeper member → search Knowledge Base → auto-reply or WhatsApp escalation → log to Google Sheets → mark read
- **Zapier skill:** `Hourly Email Triage & Auto-Reply`

### Outbound B2B Campaign
- **Trigger:** Daily at 9:00 AM
- **Inbox:** outreach@justgym.co.uk
- **Flow:** SerpAPI (find businesses) → Hunter (find email) → check log (90-day window) → Claude drafts email → Gmail sends → log to Google Sheets
- **Zapier skill:** `B2B Staff Benefits Outreach`
- **Daily cap:** 20 emails maximum

---

## Tools Required

| Tool | Zapier App ID | Cost |
|---|---|---|
| Gmail | `GoogleMailV2CLIAPI` | Included in Google Workspace |
| Google Sheets | `GoogleSheetsV2CLIAPI` | Included in Google Workspace |
| WhatsApp Business | `App228834CLIAPI` | WhatsApp Business plan |
| Claude (Anthropic) | `AnthropicCLIAPI` | Pay per use |
| SerpAPI | Direct API | Free: 100/month |
| Hunter.io | `EmailHunterCLIAPI` | Free: 25/month |

---

## Key Rules

- **Sleeper members must never be contacted by any automation.** Route to human immediately.
- Auto-replies use Knowledge Base content only — no internal names, system details, or mention of AI.
- Outbound emails are capped at 20 per day. No business is contacted more than once per 90 days.
- All emails sign off as *The Support Team* (inbound) or *JustGym Team* (outbound).

---

## Supporting Documents

- `Documents/JustGym-Automations.md` — full automation overview
- `Documents/JUSTGYM/Marketing/outbound-b2b-outreach.md` — outbound campaign brief with Claude prompt template
