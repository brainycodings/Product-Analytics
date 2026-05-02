# 📊 Product Analytics Automation Pipeline
> A fully automated product analytics system built with Mixpanel, n8n, HubSpot — simulating the customer intelligence infrastructure of a B2B SaaS company.

---

## Overview

This project instruments a fictional B2B SaaS product from scratch: generating realistic behavioral events, building product analytics, and automating cross-system data reconciliation between Mixpanel and HubSpot.

Built as a portfolio project to demonstrate end-to-end product analytics and automation skills.

---

## Architecture

```
Python Script
└── Generates 200 fictional users + behavioral events
        │
        ▼
Mixpanel
└── 5 event types tracked with real timestamps
└── User profiles with plan, MRR, country
        │
        ▼
[Module 2 — Product Analytics]
Funnel analysis → Retention → Segmentation by plan
        │
        ▼
[Module 3 — Cross-system Pipeline]
n8n reads Mixpanel churners → updates HubSpot → alerts Slack
        │
        ▼
[Module 4 — Data Reconciliation]
Weekly check: active Mixpanel users vs churn_risk HubSpot contacts
→ Detects inconsistencies → Slack report
```

---

## Tech Stack

| Tool | Role |
|------|------|
| **Mixpanel** | Product analytics & behavioral tracking |
| **Python** | Event generation & API calls |
| **n8n** | Workflow automation |
| **HubSpot** | CRM — contact storage & segmentation |
| **Slack** | Alerts & weekly reports |

---

## Event Tracking Model

5 events tracked for 200 fictional users with realistic behavioral distribution:

| Event | Description | Trigger |
|-------|-------------|---------|
| `user_signed_up` | User created an account | Once per user |
| `session_started` | User opened the app | 1–20x depending on churn status |
| `feature_used` | User interacted with a feature | During active sessions |
| `report_generated` | User exported a report | Pro/Enterprise users only |
| `invite_sent` | User invited a teammate | Expansion signal |

**Churn distribution:** 25% of users are flagged as churning — fewer sessions, no feature usage, no invites.

---
## Funnel Analysis Results

Funnel: `session_started` → `feature_used` → `invite_sent`

| Step | Conversion |
|------|-----------|
| session_started → feature_used | 90.51% |
| feature_used → invite_sent | 7.69% |

**Key insight:** The critical drop is between `feature_used` and `invite_sent`. 92% of active users never invite a teammate — a targeted automation triggered after 3 sessions without an invite could significantly improve expansion rate.

---

## Module Details

### Module 1 — Event Ingestion
- Generates 200 user profiles with realistic properties
- Sends 5 event types with historical timestamps via Mixpanel `/import` API
- Handles EU datacenter routing and batch sending
- Uploads user profiles via `/engage` endpoint

### Module 2 — Product Analytics (Mixpanel)
- Funnel: activation rate from session to feature usage to invite
- Segmentation: session count broken down by plan (free/starter/pro/enterprise)
- Retention analysis: user return rate over time

### Module 3 — Cross-system Pipeline (n8n)
- Runs at 03:00am
- Fetches churning users from Mixpanel `/engage` API
- Updates matching HubSpot contacts with `churn_segment = churn_risk_mixpanel`
- Posts Slack alert for each at-risk contact

### Module 4 — Data Reconciliation (n8n)
- Runs every Monday at 09:00am
- Fetches active users from Mixpanel (is_churning = false)
- Fetches churn_risk contacts from HubSpot
- Compares the two datasets and detects inconsistencies
- Posts weekly reconciliation report to Slack

---

## Sample Reconciliation Report

```
🔍 Rapport cohérence hebdo Mixpanel ↔ HubSpot

✅ Users actifs Mixpanel : 139
🔴 Contacts churn_risk HubSpot : 62
⚠️ Incohérences détectées : 1

Contacts à corriger :
emma.simon195@example.com
```

---

## Repository Structure

```
├── README.md
├── scripts/
│   ├── generate_events.py          # Sends 200 users + events to Mixpanel
│   └── export_users_to_csv.py      # Exports Mixpanel profiles to CSV
├── workflows/
│   ├── module3_mixpanel_to_hubspot.json
│   └── module4_reconciliation.json
└── screenshots/
    ├── mixpanel_funnel.png
    ├── mixpanel_segmentation.png
    ├── module3_slack_alert.png
    └── module4_reconciliation_report.png
```

---

## How to Run

1. Create a Mixpanel account → select **EU servers** → get Project Token and API Secret
2. Run `generate_events.py` to populate Mixpanel with 200 users and events
3. Run `export_users_to_csv.py` to export profiles for HubSpot import
4. Import CSV into Google Sheets → run n8n Workflow 1 to populate HubSpot
5. Import n8n workflow JSONs and configure credentials
6. Activate workflows and execute manually to test

---

## Why This Project Matters

This project demonstrates the full customer intelligence loop:

1. **Instrument** a product with behavioral tracking
2. **Analyze** user behavior to identify activation and churn patterns
3. **Automate** cross-system data sync between analytics and CRM
4. **Reconcile** data inconsistencies between two sources of truth

---

nly. No real user data was collected or stored.*
