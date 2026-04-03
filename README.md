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

## Setup

### What you need before starting

- Claude Code installed
- A Notion account (free tier works)
- A Zapier account (Starter plan or above for multi-step zaps)
- Microsoft 365 / Outlook (for sending emails)
- Calendly (any paid plan that supports webhooks)
- Stripe (for payment conversion detection)

---

## Step 1 — Install via the Claude Code Marketplace

**1a. Register this marketplace (one-time)**

Open `~/.claude/settings.json` and add:

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

**1b. Install the plugin through the UI**

1. Open Claude Code
2. Type `/plugins` to open the plugin marketplace
3. Find **fitness-studio-ai** in the marketplace list
4. Click **Enable** next to the `fitness-studio` plugin
5. Restart Claude Code

The 5 skills will now be available in every conversation.

---

## Step 2 — Set up the Notion CRM database

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

3. Copy the database ID from the URL: `notion.so/YOUR-WORKSPACE/`**`this-part-is-the-database-id`**`?v=...`

### Get a Notion API key

1. Go to [notion.so/my-integrations](https://notion.so/my-integrations)
2. Click **New integration**
3. Name it "Claude CRM", select your workspace, click Submit
4. Copy the **Internal Integration Token** (starts with `secret_...`)
5. Back in your Leads CRM database, click the `...` menu → **Add connections** → select your integration

---

## Step 3 — Set up Zapier

### Get your Zapier API key (for Claude Code MCP)

1. Go to [zapier.com/app/developer](https://zapier.com/app/developer)
2. Click your profile → **Developer Platform**
3. Under **API Keys**, create a new key
4. Copy it — you'll use this when setting up the Zapier MCP server in Claude Code

### Zap 1 — Calendly → Notion (new lead intake)

1. In Zapier, click **Create Zap**
2. **Trigger:** Calendly → *Invitee Created*
   - Connect your Calendly account
   - Select the event type used for sales calls
3. **Action 1:** Notion → *Find Database Item*
   - Connect your Notion account
   - Database: Leads CRM
   - Search field: Email → map from Calendly email
4. **Action 2:** Add a Filter — only continue if item was NOT found (avoid duplicates)
5. **Action 3:** Notion → *Create Database Item*
   - Name → Calendly: Invitee Name
   - Email → Calendly: Invitee Email
   - Phone → Calendly: Invitee Phone
   - Status → `call_booked`
   - Source → `Calendly`
   - Calendly booking date → Calendly: Event Start Time

### Zap 2 — Stripe → Notion (lead converted)

1. **Trigger:** Stripe → *Payment Intent Succeeded*
   - Connect your Stripe account
2. **Action 1:** Notion → *Find Database Item*
   - Database: Leads CRM
   - Search field: Email → map from Stripe customer email
3. **Action 2:** Notion → *Update Database Item*
   - Status → `converted`
   - Converted date → today

---

## Step 4 — Connect Notion MCP to Claude Code

Add this to your `~/.claude/settings.json` under `mcpServers`:

```json
"mcpServers": {
  "notion": {
    "type": "http",
    "url": "https://mcp.notion.com/sse",
    "headers": {
      "Authorization": "Bearer YOUR_NOTION_TOKEN"
    }
  }
}
```

Replace `YOUR_NOTION_TOKEN` with the `secret_...` key from Step 2.

---

## Step 5 — Connect Outlook / Microsoft 365

Use Zapier to send emails (simplest option):

1. In Zapier, create a **Zap** triggered by a **Webhook** (catch hook)
2. Action: Microsoft 365 → *Send Email*
   - Map: To, Subject, Body from webhook payload
3. Copy the webhook URL — you'll give this to Claude as the "send email endpoint"

Or, if your organisation allows it, use the [Microsoft Graph MCP server](https://github.com/microsoft/graph-mcp) for direct Outlook access.

---

## Step 6 — Tell Claude your configuration

On first use, tell Claude:

> "My Notion Leads CRM database ID is `xxxxx`. My membership link is `https://...`. Emails should be sent from `hello@mystudio.com`."

Claude will remember this for the session. For permanent config, you can add these to a `CLAUDE.md` file in your project.

---

## Scheduling daily follow-ups

To run `lead-followup` automatically every morning, ask Claude:

> "Set up a daily schedule to run lead follow-ups at 9am"

Claude Code will create a cron schedule for you.

---

## Troubleshooting

**Leads not appearing after Calendly booking**
- Check the Zap is turned on in Zapier
- Check the Calendly event type matches the trigger
- Verify the Notion integration has access to the database

**Emails not sending**
- Check the Outlook Zapier connection is still authenticated (tokens expire)
- Verify the webhook URL is correct

**Claude can't find a lead**
- Make sure the Notion MCP is connected and the database ID is correct
- Check the lead's name spelling matches exactly
