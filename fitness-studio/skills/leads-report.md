---
name: leads-report
description: Generate a pipeline report showing the current state of all leads. Trigger when asked for a leads summary, pipeline update, conversion report, or "how are the leads looking".
---

# Leads Pipeline Report

Query the Notion CRM and produce a clear pipeline summary.

## Step 1 — Fetch all leads from Notion

Get every lead record regardless of status.

## Step 2 — Build the pipeline summary

Group leads by status and report:

### Pipeline Overview
| Status | Count |
|--------|-------|
| call_booked | n |
| call_done | n |
| link_sent | n |
| converted | n |
| cold | n |
| **Total** | **n** |

### Conversion Rate
- Leads who reached `link_sent` → converted: **X%**
- Overall (all leads → converted): **X%**

### Needs Attention
List any leads in `link_sent` who are overdue for a follow-up (based on the schedule in lead-followup). Flag them by name and how many days overdue.

### Recent Conversions (last 7 days)
List names and converted date.

### Gone Cold This Week
List names who moved to `cold` in the last 7 days.

### Oldest Open Leads
List the top 3 leads who have been in `link_sent` the longest.

## Step 3 — Plain English summary

After the tables, write 2–3 sentences summarising the overall health of the pipeline. Flag anything that needs immediate action.
