# Zapier Zap Configuration Reference

## Zap Name
`HR Candidate Intake Automation`

## Overview
- **Type:** Multi-step Zap (1 Trigger + 2 Actions)
- **Plan required:** Free tier (Zapier supports 2-step Zaps on free; this uses 3 steps — upgrade to Starter if needed or use Google Sheets as intermediary)
- **Status:** Published / On

---

## Step 1 — Trigger

| Setting | Value |
|---|---|
| App | Google Forms |
| Event | New Form Response |
| Account | your-google-account@gmail.com |
| Spreadsheet | HR Candidate Tracker |
| Worksheet | Candidates |

**Test:** Submit a dummy form entry before clicking "Test trigger". Zapier pulls the most recent row.

---

## Step 2 — Action 1: Data Storage

| Setting | Value |
|---|---|
| App | Google Sheets |
| Event | Create Spreadsheet Row |
| Account | your-google-account@gmail.com |
| Spreadsheet | HR Candidate Tracker |
| Worksheet | Candidates |

### Field Mapping

| Column | Mapped To | Type |
|---|---|---|
| A — Timestamp | `{{trigger.timestamp}}` | Dynamic |
| B — Full Name | `{{trigger.full_name}}` | Dynamic |
| C — Email | `{{trigger.email_address}}` | Dynamic |
| D — Role Applied | `{{trigger.role_applied_for}}` | Dynamic |
| E — Status | `New` | Static value |

---

## Step 3 — Action 2: Notification

| Setting | Value |
|---|---|
| App | Gmail |
| Event | Send Email |
| From | your-google-account@gmail.com |
| To | hr-team@yourcompany.com |
| Subject | `🆕 New Application: {{role_applied_for}} — {{full_name}}` |
| Reply-To | `{{email_address}}` |
| Body type | Plain text |

### Email Body
```
Hi HR Team,

A new job application has been submitted. Please review the candidate details below.

---
Name:    {{full_name}}
Email:   {{email_address}}
Role:    {{role_applied_for}}
Applied: {{timestamp}}
Status:  New
---

Open the candidate tracker here:
https://docs.google.com/spreadsheets/d/YOUR_SHEET_ID

This is an automated notification. Reply to this email to contact the candidate directly.
```

---

## Zap History — Expected Output

On each successful run:
```
✅ Trigger fired       — New Google Forms response detected     ~0s
✅ Action 1 success    — Row created in HR Candidate Tracker    ~2s
✅ Action 2 success    — Email sent via Gmail                   ~4s
```

---

## Error Handling

- Enable Zapier error notifications: `Settings → Notifications → Email me when a Zap fails`
- If trigger doesn't fire: check that the Google Form is linked to the correct spreadsheet and the tab is named `Candidates`
- If Sheets row is missing columns: re-check field mapping — ensure dynamic variables are selected from the trigger dropdown, not typed manually
- If email not received: check spam folder; verify Gmail account is authorised in Zapier
