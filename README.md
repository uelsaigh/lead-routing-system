# Lead Routing & Priority Assignment System

## Overview
A deterministic lead routing system built in n8n that assigns **exactly one team** and **one priority level** to every incoming lead, then logs the decision back to the source of truth.

The system replaces manual lead triage with consistent, rule-based routing that runs automatically and leaves a clear audit trail.

📸 *Screenshot: n8n workflow canvas (full workflow overview)*

---

## Problem
Manual lead routing created operational risk and inconsistency:

- Leads were not reviewed immediately
- Urgent inquiries were mixed with low-intent messages
- Routing decisions varied depending on who saw the lead first
- No record existed explaining why a lead was assigned a certain way

The core issue was that routing logic lived in people’s heads instead of a system.

📸 *Screenshot: Google Sheet showing new leads with empty routing fields*

---

## Solution
A single-path routing engine that:

- Evaluates each lead once
- Assigns one team and one priority
- Writes decisions back to the same Google Sheets row
- Prevents duplicate processing

All decision logic is computed first, then executed once. No parallel actions, no duplicate notifications.

📸 *Screenshot: Google Sheet with routing columns populated*

---

## System Architecture
High-level flow:

1. New lead added to Google Sheets
2. n8n workflow triggers
3. JavaScript Function node computes:
   - assigned_team
   - priority_level
   - routing_reason
4. Results are written back to the same row
5. (Optional) Notifications sent downstream

This **compute → act** pattern guarantees deterministic behavior and simplifies maintenance.

📸 *Screenshot: Function node showing decision logic*

---

## Tech Stack
- **n8n** — workflow orchestration
- **JavaScript** — routing and priority logic
- **Google Sheets** — lead intake and system of record
- **Slack** (optional extension) — team notifications

---

## Decision Logic Summary

### Team Assignment
Based on keywords in *Service Interest*:

- “Paid Ads” → Paid Ads
- “SEO” → SEO
- “Website” → Web
- “Social” → Social
- No match → General (fallback)

### Priority Assignment
Evaluated in order:

**High**
- Message contains buying signals (pricing, quote, audit, budget)
- OR service selected is an audit

**Medium**
- Free email domain (Gmail, Yahoo, Outlook, Hotmail)
- OR missing phone number

**Low**
- All other cases

Each lead matches **one and only one** outcome.

---

## Outcome
- Every lead is routed within seconds
- High-intent leads are clearly flagged
- No duplicate assignments or notifications
- All decisions are logged and reviewable

The system behaves the same way every time for the same input.

---

## Success Criteria
- One execution per lead
- One assigned team per lead
- One priority per lead
- Routing decision written back to source sheet
- Safe fallback for unknown or incomplete data

---

## Edge Cases Considered
- Missing service interest
- Missing phone number
- Consumer email domains
- Messages without keywords
- Unexpected or new service values
- Duplicate workflow triggers

All cases resolve to a valid routing outcome without manual intervention.

---

## Screenshots to Include
1. n8n workflow canvas (architecture overview)
2. Google Sheet before routing (empty decision columns)
3. Google Sheet after routing (assigned team + priority)
4. Function node with JavaScript logic
5. (Optional) Slack notification example
