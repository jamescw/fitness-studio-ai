# Fitness Studio AI

Claude Code skills for lead nurturing and CRM automation in fitness studios. Connects Calendly, Notion, Stripe and Outlook into an automated pipeline — no code required.

## What it does

When a lead books a call via Calendly, this system:
1. Creates a lead record in Notion automatically
2. Lets you log the call outcome by just telling Claude what happened
3. Sends a personalised membership email immediately
4. Follows up automatically over 1, 3, 7, and 14 days if they don't convert
5. Marks them converted the moment a Stripe payment lands
6. Gives you a pipeline report on demand

## Skills included

| Skill | How to trigger |
|-------|---------------|
| `call-done` | "Just finished a call with Sarah, she's interested but wants to think about it" |
| `lead-followup` | "Check on leads" or runs on a daily schedule |
| `lead-converted` | Automatic via Stripe webhook, or "Sarah just signed up" |
| `lead-cold` | Automatic after 4 follow-ups |
| `leads-report` | "How are the leads looking?" |

---

## What you need before starting

- Claude Code (CLI or Desktop app)
- A Notion account (free tier works)
- A Zapier account (Starter plan or above for multi-step zaps)
- Microsoft 365 / Outlook (for sending emails)
- Calendly (any paid plan that supports webhooks)
- Stripe (for payment conversion detection)

---

## Installation

### Option A — Claude Code Desktop App

1. Open the Claude Code Desktop app
2. Go to **Settings → Integrations → Notion** and connect your Notion account
3. Go to **Extensions / Plugins → Add Marketplace**
4. Enter this repository URL: `https://github.com/jamescw/fitness-studio-ai`
5. Find **fitness-studio** in the list and click **Install**
6. Claude will prompt you for your configuration values — enter each one when asked

### Option B — Claude Code CLI

**Step 1 — Register this marketplace (one-time)**

Add to `~/.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "fitness-studio-ai": {
      "source": {
        "source": "github",
        "repo": "jamescw/fitness-studio-ai"
      }
    }
  }
}
```

**Step 2 — Connect Notion**

In Claude Code, run:
```
/mcp add notion
```
Or go to **Settings → MCP Servers** and enable the built-in Notion connector.

**Step 3 — Install the plugin**

```bash
claude plugin install fitness-studio@fitness-studio-ai
```

Claude will prompt you for your configuration values — enter each one when asked.

**To update later:**
```bash
claude plugin update fitness-studio@fitness-studio-ai
```

### Configuration values you'll be prompted for

| Value | Where to find it |
|-------|-----------------|
| Notion API token | [notion.so/my-integrations](https://notion.so/my-integrations) → New integration → copy the `secret_...` token |
| Notion database ID | Open your Leads CRM in Notion → copy the ID from the URL |
| Membership link | The URL you send leads to sign up |
| Studio email | The From address for emails (e.g. `hello@mystudio.com`) |
| Zapier email webhook | Created in Step 3 of Zapier setup below |

---

## Step 1 — Set up the Notion CRM database

1. Go to [notion.so](https://notion.so) and create a new database called **Leads CRM**
2. Add the following properties (exact names matter):

| Property name | Type |
|--------------|------|
| Name | Title |
| Email | Email |
| Phone | Phone |
| Source | Select (options: Calendly, Walk-in, Referral) |
| Status | Select (options: new, call_booked, call_done, link_sent, converted, cold) |
| Calendly booking date | Date |
| Call date | Date |
| Link sent date | Date |
| Follow up count | Number |
| Last followup date | Date |
| Cold date | Date |
| Converted date | Date |
| Notes | Text |
| Assigned to | Person |

3. Copy the database ID from the URL:
   `notion.so/YOUR-WORKSPACE/`**`this-part-is-the-database-id`**`?v=...`

4. Connect your integration: click `...` menu → **Add connections** → select your integration

---

## Step 2 — Set up Zapier

### Zap 1 — Calendly → Notion (new lead intake)

1. In Zapier, click **Create Zap**
2. **Trigger:** Calendly → *Invitee Created*
   - Connect your Calendly account and select your sales call event type
3. **Action 1:** Notion → *Find Database Item*
   - Database: Leads CRM · Search field: Email
4. **Action 2:** Filter — only continue if item was NOT found (avoids duplicates)
5. **Action 3:** Notion → *Create Database Item*
   - Name → Calendly: Invitee Name
   - Email → Calendly: Invitee Email
   - Phone → Calendly: Invitee Phone
   - Status → `call_booked`
   - Source → `Calendly`
   - Calendly booking date → Calendly: Event Start Time

### Zap 2 — Stripe → Notion (lead converted)

1. **Trigger:** Stripe → *Payment Intent Succeeded*
2. **Action 1:** Notion → *Find Database Item* by email
3. **Action 2:** Notion → *Update Database Item*
   - Status → `converted`
   - Converted date → today

### Zap 3 — Webhook → Outlook (email sender)

1. **Trigger:** Webhooks by Zapier → *Catch Hook*
2. Copy the webhook URL — this is your **Zapier email webhook** value for the plugin config
3. **Action:** Microsoft 365 → *Send Email*
   - To → webhook payload: `to`
   - From → webhook payload: `from`
   - Subject → webhook payload: `subject`
   - Body → webhook payload: `body`

---

## Scheduling daily follow-ups

To run `lead-followup` automatically every morning, tell Claude:

> "Set up a daily schedule to run lead follow-ups at 9am"

Claude Code will create a cron schedule for you.

---

## Troubleshooting

**Leads not appearing after Calendly booking**
- Check the Zap is turned on in Zapier
- Verify the Notion integration has access to the Leads CRM database

**Emails not sending**
- Check the Outlook connection in Zapier hasn't expired (reconnect if needed)
- Verify the webhook URL matches what's in your plugin config

**Claude can't find a lead**
- Make sure Notion is connected (Desktop: Settings → Integrations, CLI: `/mcp`)
- Check the lead's name spelling matches the Notion record
