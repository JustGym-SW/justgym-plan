# b2b staff benefits outreach

Daily: find local businesses via Apollo, get contact emails via Hunter, have Claude draft personalised gym membership pitches, send via Gmail, and log everything to Google Sheets.

---

# B2B Staff Benefits Outreach — JustGym

## Purpose
Run every day at 9:00 AM. Find local businesses, locate their contact email, have Claude write a personalised outreach email, send it via Gmail, and log every attempt in Google Sheets.

---

## RULES — Read Before Executing

1. **Daily cap:** Maximum 20 businesses per run. Stop at 20 even if Apollo returns more.
2. **No repeat contact:** Before sending, check the Google Sheets log. Skip any business contacted in the past 90 days.
3. **Minimum size:** Skip businesses with fewer than 5 employees.
4. **Dedicated sender:** All emails go from the JustGym outreach Gmail account — not the main support inbox.
5. **No pricing in emails:** Claude must not include specific pricing, guarantees, or anything that sounds templated.

---

## Step 1 — Find Local Businesses (Apollo)

```mcp-tool
ApolloCLIAPI
```

Search Apollo for businesses matching this profile:
- **Location:** Within target radius of gym (configure your postcode/area in Apollo)
- **Employee count:** 5–200
- **Industries:** Office/professional services, retail, hospitality, healthcare, tech
- **Contact title:** HR Manager, Office Manager, Managing Director, Director, Owner

Collect per business:
- Business Name
- Industry
- Employee Count
- Decision Maker: Name, Job Title

---

## Step 2 — Find Contact Email (Hunter)

```mcp-tool
EmailHunterCLIAPI
```

For each business from Step 1, use Hunter to find a verified email address for the decision maker.

- If a **verified email is found** → proceed to Step 3
- If **no email found** → log row in Google Sheets with Status = `No Email Found`, skip to next business

---

## Step 3 — Check for Recent Contact (Google Sheets)

```mcp-tool
GoogleSheetsV2CLIAPI
```

Search the outreach log spreadsheet for the business name or contact email.

- If contacted within the **last 90 days** → skip, log Status = `Already Contacted (Recent)`, move to next
- If not in log, or last contact was 90+ days ago → proceed to Step 4

---

## Step 4 — Claude Drafts the Email

```mcp-tool
AnthropicCLIAPI
```

Send the following prompt to Claude. Replace the `{{variables}}` with real values from Apollo/Hunter:

**System prompt:**
```
You are a friendly outreach specialist for JustGym, a local gym offering corporate staff membership packages to businesses as an employee benefit. Write concise, warm, human-sounding cold outreach emails. Never sound salesy or templated.
```

**User prompt:**
```
Write a short outreach email to the following contact on behalf of JustGym:

Business: {{business_name}}
Contact Name: {{contact_name}} (use "there" if unknown)
Industry: {{industry}}
Approx. employees: {{employee_count}}

Requirements:
- Under 150 words
- Open with a personalised line referencing their industry or team size
- Briefly explain: staff benefit gym memberships, team wellness, affordable monthly rate, a genuine employee perk
- End with a soft CTA: "happy to send over our packages — no obligation"
- Sign off: JustGym Team
- No pricing, no specific numbers, no guarantees
- No emojis
- Output the email body only — no subject line
```

Save Claude's output as `drafted_email_body`.

---

## Step 5 — Send via Gmail

```mcp-tool
GoogleMailV2CLIAPI
```

Send the email using these fields:
- **From:** JustGym outreach Gmail account
- **To:** `{{contact_email}}` from Hunter
- **Subject:** `A staff perk your team will actually use — JustGym`
- **Body:** `{{drafted_email_body}}` from Claude

---

## Step 6 — Log to Google Sheets

```mcp-tool
GoogleSheetsV2CLIAPI
```

Append a new row to the outreach log spreadsheet:

| Column | Value |
|---|---|
| Date & Time | Current timestamp |
| Business Name | From Apollo |
| Industry | From Apollo |
| Employee Count | From Apollo |
| Contact Name | From Hunter/Apollo |
| Contact Email | From Hunter |
| Email Sent | `Yes` |
| Status | `Contacted` |

---

## Step 7 — Repeat

Loop back to Step 2 for the next business from Apollo until either:
- All businesses from Step 1 are processed, or
- The daily cap of **20 sent emails** is reached — whichever comes first

---

## Required Zapier Apps

| App | App ID | Used For |
|---|---|---|
| Apollo | `ApolloCLIAPI` | Find local businesses & decision makers |
| Hunter | `EmailHunterCLIAPI` | Find verified contact emails |
| Anthropic (Claude) | `AnthropicCLIAPI` | Draft personalised outreach emails |
| Gmail | `GoogleMailV2CLIAPI` | Send outreach emails |
| Google Sheets | `GoogleSheetsV2CLIAPI` | Log all outreach activity |

## Note on Claude API Key
Zapier connects to Claude (Anthropic) using an API key — not a separate login. Get your key at console.anthropic.com (it ties to your Anthropic account, same as your Claude login). Paste it once into Zapier's Anthropic connection and you're set.

## Schedule
Runs **daily at 9:00 AM** via Zapier Schedule trigger.
