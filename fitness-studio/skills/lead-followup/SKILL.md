---
name: lead-followup
description: Daily lead follow-up agent. Checks the Notion CRM for leads who received a membership link but haven't converted, and sends personalised follow-up emails. Run this on a schedule or when asked to check on leads.
---

# Lead Follow-Up Agent

You are running the daily lead nurturing check. Work through every cold lead systematically.

## Step 1 — Query Notion

Fetch all leads from the Notion CRM where:
- `status` = `link_sent`

## Step 2 — Determine who needs a follow-up

For each lead, calculate days since `link_sent_date`. Apply this schedule:

| follow_up_count | Days since link sent | Action |
|----------------|---------------------|--------|
| 0 | ≥ 1 day | Send follow-up #1 |
| 1 | ≥ 3 days | Send follow-up #2 |
| 2 | ≥ 7 days | Send follow-up #3 |
| 3 | ≥ 14 days | Send follow-up #4 (final) |
| 4 | any | Mark cold — invoke lead-cold skill |

Skip leads who don't meet the day threshold yet.

## Step 3 — Generate personalised follow-up emails

For each lead due a follow-up, write an email that:

- References something specific from their `notes` field — never send a generic email
- Matches the tone to the follow-up number:
  - **#1** — Light check-in. "Just wanted to make sure the link came through..."
  - **#2** — Add a little social proof or urgency. "We've had a few people start this week..."
  - **#3** — Address likely objection. Use the notes to infer what held them back (cost, timing, uncertainty) and speak to it directly
  - **#4** — Keep the door open. Warm, no pressure, "whenever you're ready"
- Subject lines must feel personal. Vary them — never reuse the same subject across follow-ups
- Keep under 120 words

## Step 4 — Send each email via Outlook

POST each email to the Zapier webhook at ${user_config.zapier_email_webhook}:
```json
{
  "to": "<lead email>",
  "from": "${user_config.studio_email}",
  "subject": "<subject>",
  "body": "<email body>"
}
```
Send each lead individually. Do not batch or CC multiple leads.

## Step 5 — Update Notion for each lead

After each send:
- Increment `follow_up_count` by 1
- Set `last_followup_date` → today

If `follow_up_count` reaches 4, also update `status` → `cold` and note the date.

## Step 6 — Report back

After completing all leads, summarise:
- How many follow-ups sent
- Names and follow-up number for each
- How many leads moved to `cold`
- Any leads you skipped and why
