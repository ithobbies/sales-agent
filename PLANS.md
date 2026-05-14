# PLANS.md

## 1. Product Goal

Build an OLX-first AI sales growth system for selling USB LED COB strips with minimal manual routine.

The system should:

- connect to OLX from the start
- import listings
- read customer messages
- collect listing metrics
- generate safe sales reply drafts
- propose improvements and growth hypotheses
- request needed photo/video assets
- send reports and approval requests through Telegram
- keep risky business actions behind approval gates

---

## 2. Development Philosophy

Build a production-shaped MVP, not a throwaway script.

Principles:

- backend-first
- OLX integration early
- Telegram as primary interface
- SQLite for MVP
- Django Admin for configuration
- React dashboard later
- modular monolith before microservices
- approval-first for risky actions
- one task at a time
- tests and self-review before moving on

---

## 3. Milestone Overview

### M1 — OLX Connected Backend MVP

Outcome:

The system connects to OLX, imports listings, polls conversations/messages, stores core data in SQLite, and sends Telegram approval notifications.

Must include:

- Django project
- SQLite configuration
- core models
- OLX OAuth skeleton
- OLX listing import service
- OLX message polling service
- Telegram bot integration
- ApprovalRequest workflow
- basic daily report

Must not include:

- React dashboard
- automatic sending of AI replies
- automatic publishing
- automatic price changes
- complex A/B experiment engine

---

### M2 — AI Drafting and Policy Layer

Outcome:

The system generates safe draft replies and listing improvement drafts, but does not execute risky actions automatically.

Must include:

- OpenAI integration service
- structured output schemas
- Sales Chat Agent
- Policy & Risk Guard Agent
- AgentRun logging
- Telegram approval for generated replies
- deterministic product-claim checks
- no automatic customer replies unless explicitly enabled later

---

### M3 — Metrics, Hypotheses, and Creative Requests

Outcome:

The system analyzes listing performance, proposes sales hypotheses, and requests targeted photo/video assets.

Must include:

- Metrics Analyst Agent
- Hypothesis Agent
- Listing Creator Agent
- Creative Director Agent
- metric snapshots
- experiment records
- photo/video request workflow
- weekly report
- comparison of listing versions

---

### M4 — Semi-Autopilot

Outcome:

The system can automatically handle safe repetitive replies and routine reporting while keeping risky actions under approval.

Must include:

- safe intent classifier
- auto-reply allowlist
- user-configurable kill switch
- audit logs for auto-actions
- allowed discount rules
- escalation rules
- monitoring of failed/uncertain replies

---

### M5 — Dashboard and Advanced Analytics

Outcome:

A React dashboard provides deeper visibility into listings, messages, hypotheses, and experiments.

Must include:

- React + TypeScript + Vite
- Tailwind CSS
- dashboard KPI cards
- listing tables
- metric charts
- hypothesis board
- approval queue
- conversation viewer
- settings pages

---

## 4. Milestone M1 Task Sequence

Recommended order:

1. `TASK-001` — Initialize Django project
2. `TASK-002` — Add core domain models
3. `TASK-003` — Add environment/settings structure
4. `TASK-004` — Add OLX OAuth skeleton
5. `TASK-005` — Add OLX API client foundation
6. `TASK-006` — Import OLX listings
7. `TASK-007` — Poll OLX conversations/messages
8. `TASK-008` — Add Telegram bot foundation
9. `TASK-009` — Add ApprovalRequest Telegram flow
10. `TASK-010` — Add daily report skeleton
11. `TASK-011` — Add Celery/Celery Beat schedules
12. `TASK-012` — Add deployment skeleton

Do not start M2 until M1 is functional enough to read real OLX listings/messages in a controlled environment.

---

## 5. Milestone M2 Task Sequence

1. `TASK-013` — Add OpenAI integration service
2. `TASK-014` — Add structured output schemas
3. `TASK-015` — Add AgentRun logging
4. `TASK-016` — Implement Policy & Risk Guard Agent
5. `TASK-017` — Implement Sales Chat Agent draft replies
6. `TASK-018` — Connect draft replies to ApprovalRequest
7. `TASK-019` — Add prompt/version management
8. `TASK-020` — Add tests for safe/unsafe reply handling

---

## 6. Milestone M3 Task Sequence

1. `TASK-021` — Store listing metric history
2. `TASK-022` — Implement Metrics Analyst Agent
3. `TASK-023` — Implement Hypothesis Agent
4. `TASK-024` — Implement Listing Creator Agent
5. `TASK-025` — Implement Creative Director Agent
6. `TASK-026` — Add CreativeAsset and CreativeRequest flows
7. `TASK-027` — Add Experiment model/workflow
8. `TASK-028` — Add weekly report

---

## 7. Major Architecture Guardrails

Do not change these without updating `DECISIONS.md`:

- Django monolith
- SQLite for MVP
- OLX integration in M1
- Telegram as primary control interface
- ApprovalRequest for risky actions
- React dashboard postponed
- all integrations isolated behind service layers
- AI outputs checked by Policy Guard
