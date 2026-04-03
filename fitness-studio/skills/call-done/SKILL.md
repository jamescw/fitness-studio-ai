---
name: call-done
description: Log a completed sales call with a lead and send them the membership link email. Trigger when the user says they just finished a call, spoke to someone, or completed a consultation.
---

# Call Done — Log & Send Membership Email

You have just been told that a sales call with a lead has been completed. Follow these steps exactly.

## Step 1 — Extract call details

From the user's message, identify:
- **Lead name** (required — ask if not mentioned)
- **Call notes** — anything the user shared: interest level, objections, what was discussed, timing, price concerns, etc. Use verbatim if possible.

## Step 2 — Find the lead in Notion

Search the Notion CRM database for a lead matching the name with status `call_booked` or `new`.

- If found: proceed to Step 3
- If not found: create a new lead record with status `call_done` and today's date, then continue
- If multiple matches: ask the user which one

## Step 3 — Update the lead record in Notion

Update the following fields:
- `status` → `call_done`
- `call_date` → today's date
- `notes` → append the call notes (don't overwrite existing notes)

## Step 4 — Generate the membership email

Write a personalised email to the lead. The email must:

- Open with their name and reference something specific from the call (use the notes)
- Be warm, human and enthusiastic — not templated or salesy
- Briefly remind them why they said they were interested
- Include the membership link: **ask the user for this if you don't have it**, or use the default link if one is configured
- Be concise — no more than 150 words
- Subject line should feel personal, not generic (avoid "Following up on our call")

Do NOT use placeholder text. If you don't have enough context to personalise, ask the user one quick question before sending.

## Step 5 — Send via Outlook

Send the email using the Microsoft 365 / Outlook integration. The From address should be the studio's configured email.

## Step 6 — Update Notion

Update the lead record:
- `status` → `link_sent`
- `link_sent_date` → today's date
- `follow_up_count` → 0

## Step 7 — Confirm

Tell the user: "Done — email sent to [Name] at [email]. I've updated their status to 'link sent' in the CRM."
