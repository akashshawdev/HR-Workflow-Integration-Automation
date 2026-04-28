# HR Workflow Automation — Low-Code Recruitment Process

![Automation](https://img.shields.io/badge/Automation-Zapier-FF4A00?style=flat-square)
![Forms](https://img.shields.io/badge/Input-Google%20Forms-4285F4?style=flat-square)
![Storage](https://img.shields.io/badge/Storage-Google%20Sheets-0F9D58?style=flat-square)
![Notify](https://img.shields.io/badge/Notify-Gmail-EA4335?style=flat-square)
![Cost](https://img.shields.io/badge/Cost-Free-1D9E75?style=flat-square)

A simulated internal HR system that automates candidate data capture, storage, and notifications using Zapier and Google Workspace tools. Built as part of an MSc Software Engineering portfolio to demonstrate real-world business process automation and Talent Acquisition workflow design.

---

## Problem Statement

Manual recruitment intake processes are slow and error-prone. HR teams waste time copy-pasting candidate data from emails into spreadsheets, and notification delays mean candidates wait longer for responses. This project replaces the manual process with a fully automated pipeline that runs without human intervention.

---

## System Flow

```
Candidate fills form → Zapier detects submission → Row added to Sheets → HR email sent → HR reviews tracker
```

No manual steps. The entire pipeline executes automatically within seconds of form submission.

---

## Tools Used

| Tool | Role | Cost |
|---|---|---|
| Google Forms | Candidate input interface | Free |
| Zapier | No-code automation engine (trigger + actions) | Free tier |
| Google Sheets | Candidate database / ATS tracker | Free |
| Gmail | HR notification delivery | Free |

---

## Architecture

```
┌─────────────────┐     submit      ┌──────────────────────────────────┐
│  Google Form    │ ─────────────→  │           Zapier Zap              │
│                 │                 │                                    │
│ • Full Name     │                 │  TRIGGER: New Form Response        │
│ • Email Address │                 │      ↓                             │
│ • Role Applied  │                 │  ACTION 1: Google Sheets           │
└─────────────────┘                 │    → Append row to Candidates tab  │
                                    │      ↓                             │
                                    │  ACTION 2: Gmail                   │
                                    │    → Send HR notification email    │
                                    └──────────────────────────────────┘
                                              ↓                ↓
                                    ┌──────────────┐  ┌──────────────────┐
                                    │ Google Sheets│  │   HR Inbox        │
                                    │ (Candidates) │  │ (Gmail alert)     │
                                    └──────────────┘  └──────────────────┘
```

---

## Google Form Setup

**Form title:** `Job Application Form`
**Confirmation message:** `Thank you! We'll be in touch soon.`

| Field | Type | Required |
|---|---|---|
| Full Name | Short answer | Yes |
| Email Address | Short answer | Yes |
| Role Applied For | Dropdown | Yes |

**Dropdown options for Role Applied For:**
- Software Engineer (Working Student)
- QA Automation Engineer
- Data Analyst
- AI/ML Engineer
- Other

**Critical:** After creating the form, link it to Google Sheets via the Sheets icon (top right). Name the spreadsheet `HR Candidate Tracker` and rename the response tab from `Form Responses 1` to `Candidates`.

---

## Google Sheets Structure

**Spreadsheet:** `HR Candidate Tracker`
**Worksheet tab:** `Candidates`

| A — Timestamp | B — Full Name | C — Email | D — Role Applied | E — Status |
|---|---|---|---|---|
| 2026-04-28 10:34 | Priya Sharma | priya.sharma@email.com | Software Engineer (WS) | New |
| 2026-04-28 11:02 | Jonas Weber | jonas.weber@email.com | QA Automation Engineer | New |
| 2026-04-28 11:47 | Amara Diallo | amara.diallo@email.com | Data Analyst | New |

**Notes:**
- Freeze Row 1 (`View → Freeze → 1 row`) to keep headers visible
- Protect Row 1 (`Data → Protect sheets and ranges`) to prevent accidental edits
- Column E `Status` is auto-set to `New` by Zapier — HR manually updates to `Under Review`, `Shortlisted`, or `Rejected`

---

## Zapier Zap Configuration

**Zap name:** `HR Candidate Intake Automation`

### Step 1 — Trigger
```
App:         Google Forms
Event:       New Form Response
Spreadsheet: HR Candidate Tracker
Worksheet:   Candidates
```

### Step 2 — Action 1 (Data Storage)
```
App:          Google Sheets
Event:        Create Spreadsheet Row
Spreadsheet:  HR Candidate Tracker
Worksheet:    Candidates
Column A:     {{trigger.timestamp}}
Column B:     {{trigger.full_name}}
Column C:     {{trigger.email_address}}
Column D:     {{trigger.role_applied_for}}
Column E:     New   ← static value, not mapped
```

### Step 3 — Action 2 (Notification)
```
App:      Gmail
Event:    Send Email
To:       hr-team@yourcompany.com
Subject:  🆕 New Application: {{role_applied_for}} — {{full_name}}
Reply-To: {{email_address}}
```

**Email body template:**
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

## How to Recreate This Workflow

1. **Create Google Form** — add 3 fields as described above, mark all required, set confirmation message
2. **Link to Sheets** — name spreadsheet `HR Candidate Tracker`, rename tab to `Candidates`, add header row with 5 columns
3. **Submit a test entry** — Zapier needs at least one existing response to pull as sample trigger data
4. **Create Zapier account** — go to zapier.com, sign up free
5. **Create new Zap** — Trigger: Google Forms → New Response → connect Google account → select spreadsheet and worksheet → test trigger
6. **Add Action 1** — Google Sheets → Create Spreadsheet Row → map all 5 columns → set Status to static `New` → test action
7. **Add Action 2** — Gmail → Send Email → configure To, Subject (with variables), Reply-To (candidate email variable), paste email body → test action
8. **Verify** — check Sheets for new row, check inbox for notification email
9. **Publish Zap** — toggle from Draft to On → share form link with candidates

---

## Sample Data

Three sample candidates used for testing:

| Name | Email | Role |
|---|---|---|
| Priya Sharma | priya.sharma@test.com | Software Engineer (Working Student) |
| Jonas Weber | jonas.weber@test.com | QA Automation Engineer |
| Amara Diallo | amara.diallo@test.com | Data Analyst |

---

## Project Outcomes

- Zero manual data entry after initial setup
- Candidate data stored and searchable in real time
- HR team notified within seconds of each application
- Reply-To field enables direct candidate communication from the notification email
- Status column provides basic ATS (Applicant Tracking System) functionality
- Entire stack uses free-tier tools — no cost to run

---

## Repository Structure

```
hr-workflow-automation/
│
├── README.md                   ← This file
├── docs/
│   ├── system-architecture.png ← Architecture diagram (export from project)
│   └── flow-diagram.png        ← End-to-end flow diagram
├── templates/
│   └── candidate-tracker.csv   ← Google Sheets column template
├── zapier/
│   └── zap-config.md           ← Full Zap configuration reference
├── samples/
│   └── sample-candidates.csv   ← Sample candidate data
└── screenshots/
    ├── google-form.png          ← Form as candidates see it
    ├── sheets-tracker.png       ← Candidate database with sample rows
    └── hr-notification-email.png← Sample notification email received
```

---

## Skills Demonstrated

- Business process automation (no-code / low-code)
- Workflow design and systems thinking
- Google Workspace integration
- Zapier trigger-action pipeline configuration
- Data mapping and field normalisation
- Applicant Tracking System (ATS) design
- Technical documentation

---

## Author

**Akash Kumar Shaw**
MSc Software Engineering — University of Europe for Applied Sciences, Germany
GitHub: [akashshawdev](https://github.com/akashshawdev)
