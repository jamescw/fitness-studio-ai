---
name: lead-converted
description: Mark a lead as converted when they sign up and pay. Trigger when told someone has signed up, paid, or completed their membership purchase. Also triggered via Zapier when a Stripe payment is received.
---

# Lead Converted

A lead has signed up and paid. Follow these steps.

## Step 1 — Identify the lead

Get the lead's email address (passed in from Stripe via Zapier, or provided by the user).

Search the Notion CRM for a matching lead by email address.

- If found: proceed
- If not found: create a new record marked `converted` (they may have come through a different channel)

## Step 2 — Update Notion

Update the lead record:
- `status` → `converted`
- `converted_date` → today's date

This stops all follow-up sequences for this lead.

## Step 3 — Send a welcome email

Write a warm, personal welcome email that:

- Congratulates them and expresses genuine excitement that they've joined
- Sets expectations for what happens next (first class, what to bring, who to ask for)
- Feels like it's from a person, not an automated system
- Is concise — under 100 words

Send via Outlook.

## Step 4 — Confirm

Report: "Lead [Name] marked as converted. Welcome email sent."
