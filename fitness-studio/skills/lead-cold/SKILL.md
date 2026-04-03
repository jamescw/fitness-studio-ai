---
name: lead-cold
description: Mark a lead as cold after the follow-up sequence ends with no response. Send a final no-pressure message and archive the lead. Triggered automatically after 4 follow-ups or manually.
---

# Lead Cold

This lead has not responded after the full follow-up sequence. Handle gracefully.

## Step 1 — Update Notion

Update the lead record:
- `status` → `cold`
- `cold_date` → today's date

## Step 2 — Send a final message

Write a brief, warm, zero-pressure final email:

- Acknowledge that the timing may not be right
- Leave the door genuinely open — no guilt, no urgency
- One sentence maximum on what they'd gain if they ever came back
- Should feel human and kind, not passive-aggressive
- Under 80 words
- Subject: keep it light, e.g. "No worries at all" or similar

POST to the Zapier webhook at ${user_config.zapier_email_webhook}:
```json
{
  "to": "<lead email>",
  "from": "${user_config.studio_email}",
  "subject": "<subject>",
  "body": "<email body>"
}
```

## Step 3 — Confirm

Report: "Lead [Name] marked as cold. Final message sent."

---

**Note:** Cold leads are not deleted. They stay in the CRM and can be reactivated if the lead gets back in touch.
