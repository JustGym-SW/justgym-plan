# hourly email triage & auto-reply

Every hour: fetch unread Gmail emails, search Knowledge Base, auto-reply or escalate to WhatsApp, and log everything to Google Sheets.

---

# Hourly Email Triage & Auto-Reply Workflow

## Purpose
Every hour, process all unread Gmail messages received in the past 60 minutes. For each email:
- Search the Knowledge Base for a relevant answer
- If found: reply automatically via Gmail and log as **Resolved Automatically**
- If not found: escalate to a human agent via WhatsApp and log as **Escalated to Human**
- Mark all processed emails as read when done

---

## IMPORTANT RULES — READ BEFORE EXECUTING

1. **Never include private or internal information in client replies.** Replies must only contain information sourced directly from the Knowledge Base. Do not mention:
   - Agent names, internal team names, or escalation processes
   - Internal system names, tools, or workflows
   - Account details, pricing, or data beyond what the Knowledge Base answer states
2. Keep replies professional, concise, and client-safe.
3. Always sign off replies generically (e.g. "Kind regards, The Support Team") — never with an individual's name.

---

## Step 1 — Fetch Unread Emails (Past Hour)

Use Gmail: Find Email to retrieve all unread messages received in the last 60 minutes.

```mcp-tool
GoogleMailV2CLIAPI
```

Parameters:
- Search query: `is:unread newer_than:1h`
- Collect for each email:
  - **Sender Name**
  - **Sender Email Address**
  - **Subject Line**
  - **Email Body** (plain text)
  - **Message ID** (needed for reply and marking read)
  - **Thread ID**
  - **Date & Time Received**

---

## Step 2 — For Each Email: Search Knowledge Base

Use your connected Knowledge Base tool to search using the email's Subject + Body content combined.

- If a **relevant answer is found** → proceed to Step 3A
- If **no relevant answer is found** → proceed to Step 3B

---

## Step 3A — Auto-Reply via Gmail (Answer Found)

```mcp-tool
GoogleMailV2CLIAPI
```

Draft and send a reply using the Knowledge Base answer. Follow these rules strictly:

**Reply format:**
```
Hi [Sender First Name],

Thank you for reaching out.

[Insert Knowledge Base answer here — verbatim or lightly paraphrased. Do NOT add any information not present in the Knowledge Base.]

If you have any further questions, please don't hesitate to get in touch.

Kind regards,
The Support Team
```

**Do NOT include:**
- Any mention of automation, AI, bots, or Zapier
- Internal reference numbers unless the Knowledge Base explicitly provides them
- Names of staff, agents, or internal departments
- Any details not sourced from the Knowledge Base

---

## Step 3B — Escalate via WhatsApp (No Answer Found)

Send a WhatsApp message to the designated human agent with the following details only:

```mcp-tool
App228834CLIAPI
```

**WhatsApp message format:**
```
🔔 NEW EMAIL — NEEDS HUMAN RESPONSE

📧 From: [Sender Name] <[Sender Email]>
📌 Subject: [Subject Line]
🕐 Received: [Date & Time]

📝 Message:
[Email Body — truncated to 500 characters if long]

Please reply directly to the customer's email.
```

---

## Step 4 — Log to Google Sheets

Append a new row to the designated Google Sheets log spreadsheet.

```mcp-tool
GoogleSheetsV2CLIAPI
```

**Columns to populate (in this exact order):**

| Column | Value |
|---|---|
| Date & Time | Timestamp of email receipt |
| Sender Name | Full name from email |
| Sender Email | Email address |
| Subject | Email subject line |
| Email Body | Full body text |
| Response Sent | If auto-replied: first 300 chars of reply. If escalated: `N/A` |
| Status | `Resolved Automatically` OR `Escalated to Human` |

**Spreadsheet details:**
- Use the existing support log spreadsheet in Google Drive
- Append — never overwrite existing rows

---

## Step 5 — Mark Email as Read

After processing each email (regardless of outcome), mark it as read in Gmail.

```mcp-tool
GoogleMailV2CLIAPI
```

Action: Add label `READ` / mark as read using the Message ID from Step 1.

---

## Step 6 — Repeat for All Emails

Loop through every email fetched in Step 1 and complete Steps 2–5 for each one before finishing the hourly run.

---

## Required Zapier Apps

| App | App ID | Used For |
|---|---|---|
| Gmail | `GoogleMailV2CLIAPI` | Fetch emails, send replies, mark read |
| Google Sheets | `GoogleSheetsV2CLIAPI` | Log all interactions |
| WhatsApp Business | `App228834CLIAPI` | Escalate to human agent |

## Schedule
This workflow runs **automatically every hour** via Zapier Schedule trigger.
