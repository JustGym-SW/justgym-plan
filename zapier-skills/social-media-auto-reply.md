# social media auto-reply

Monitor Instagram comments, Facebook Page comments, and Facebook Messenger messages. Have Claude draft a natural human-sounding reply and post it automatically. Escalate complaints, sensitive topics, and sleeper member interactions to a human agent.

---

# Social Media Auto-Reply — JustGym

## Purpose
Monitor JustGym's Instagram and Facebook for new comments and messages. Claude drafts a short, natural reply in JustGym's voice and posts it automatically. Negative comments, complaints, and sensitive interactions are escalated to the human team via WhatsApp.

---

## RULES — Read Before Executing

1. **Keep replies short.** One or two sentences for comments. A short paragraph at most for messages.
2. **No pricing in public comments.** Direct pricing questions to DMs or the website.
3. **Never auto-reply to complaints or negative sentiment.** Escalate immediately.
4. **Sleeper members** should not receive automated replies. Route to human.
5. **Never confirm or deny membership status** in any public reply.
6. **Ignore spam.** Skip accounts with no posts, no profile picture, or obvious bot patterns.
7. **Do not mention competitors by name.**

---

## Trigger A — New Instagram Comment

```mcp-tool
InstagramBusinessCLIAPI
```

Trigger when a new comment is posted on any JustGym Instagram post.

Collect:
- Post content / caption (for context)
- Comment text
- Commenter username
- Comment ID (needed to post the reply)

---

## Trigger B — New Facebook Page Comment

```mcp-tool
FacebookV2CLIAPI
```

Trigger when a new comment is posted on the JustGym Facebook Page.

Collect:
- Post content (for context)
- Comment text
- Commenter name
- Comment ID

---

## Trigger C — New Facebook Messenger Message

```mcp-tool
FacebookMessengerCLIAPI
```

Trigger when a new message is sent to the JustGym Facebook Page via Messenger.

Collect:
- Message text
- Sender name or ID

---

## Step 1 — Check Escalation Conditions

Before drafting any reply, check whether the interaction should be escalated.

**Escalate if any of the following are true:**
- Comment or message contains negative sentiment, a complaint, or a refund/cancellation request
- Message mentions injury, health concerns, or legal action
- Sender is a sleeper member (check against member database)
- Comment is from an account with no posts or no profile picture (likely spam — skip entirely)
- Claude cannot confidently answer the question from the Knowledge Base

If escalation conditions are met → skip to Step 4 (Escalate).

---

## Step 2 — Claude Drafts the Reply

```mcp-tool
AnthropicCLIAPI
```

**System prompt:**
```
You are the social media voice for JustGym, a local gym. Write short, natural replies to comments and messages. Sound like a real person — not a brand. Match the energy of the comment. Be direct and friendly. Never mention pricing in public comments. Never sound corporate or scripted.
```

**User prompt (comments):**
```
Platform: {{platform}}
Original post: {{post_content}}
Comment from @{{username}}: {{comment_text}}

Write a reply to this comment. One or two sentences. Match their energy. If they are asking about pricing or membership details, tell them to DM us or check the link in bio. Do not use emojis unless the comment used them first.
```

**User prompt (Messenger messages):**
```
Platform: Facebook Messenger
Message from {{sender_name}}: {{message_text}}

Write a friendly, direct reply. Answer the question if you can from general gym knowledge. If the question needs specific details (pricing, availability, personal account queries), let them know the team will follow up shortly. Keep it brief and warm. No formal sign-off needed.
```

---

## Step 3 — Post the Reply

**For Instagram comments:**
```mcp-tool
InstagramBusinessCLIAPI
```
Post Claude's reply as a reply to the specific comment using the Comment ID.

**For Facebook Page comments:**
```mcp-tool
FacebookV2CLIAPI
```
Post Claude's reply as a reply to the specific comment.

**For Facebook Messenger:**
```mcp-tool
FacebookMessengerCLIAPI
```
Send Claude's reply as a Messenger response to the sender.

---

## Step 4 — Escalate (if triggered)

```mcp-tool
App228834CLIAPI
```

Send a WhatsApp message to the team:

```
SOCIAL — NEEDS HUMAN REVIEW

Platform: {{platform}}
From: {{username or sender_name}}
Type: {{Comment / Message}}

Content:
{{comment or message text}}

Reason flagged: {{escalation reason}}
```

---

## Step 5 — Log to Google Sheets

```mcp-tool
GoogleSheetsV2CLIAPI
```

Append a row to the **Social Responses** sheet:

| Column | Value |
|---|---|
| Date & Time | Timestamp |
| Platform | Instagram / Facebook Page / Facebook Messenger |
| Type | Comment / Message |
| Username | Sender handle or name |
| Original Content | Comment or message text |
| Reply Sent | Claude's reply, or N/A |
| Status | Replied Automatically / Escalated / Skipped |

---

## Required Apps

| App | App ID | Used For |
|---|---|---|
| Instagram for Business | `InstagramBusinessCLIAPI` | Detect comments, post replies |
| Facebook Pages | `FacebookV2CLIAPI` | Detect comments, post replies |
| Facebook Messenger | `FacebookMessengerCLIAPI` | Detect and reply to page messages |
| Claude (Anthropic) | `AnthropicCLIAPI` | Draft all replies |
| WhatsApp Business | `App228834CLIAPI` | Escalation alerts |
| Google Sheets | `GoogleSheetsV2CLIAPI` | Log all interactions |

## Note on Instagram DMs
Meta's API does not currently support reading or sending Instagram Direct Messages via third-party tools. Instagram DMs must be handled manually through the Instagram app or Meta Business Suite.
