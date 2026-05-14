# TASKS.md

## 1. Task Rules

Codex must work on one task at a time.

Allowed statuses:

- `BACKLOG`
- `READY`
- `IN_PROGRESS`
- `CODE_REVIEW`
- `NEEDS_FIXES`
- `TESTING`
- `DONE`
- `BLOCKED`

Rules:

- Only `READY` tasks may be started.
- A task must be moved to `IN_PROGRESS` before implementation.
- A task must not be marked `DONE` unless acceptance criteria are met.
- Every completed task must update `CONTEXT.md`.
- Architecture changes require `DECISIONS.md` update.
- If a new issue is discovered, create a new follow-up task instead of expanding scope silently.
- Do not combine unrelated tasks.

---

## 2. Current Milestone

Current milestone: `M1 — OLX Connected Backend MVP`

Current next task: `TASK-001`

---

## 3. M1 — OLX Connected Backend MVP

### TASK-001 — Initialize Django project

Status: READY  
Priority: P0  
Type: backend/foundation  
Owner: Codex

Goal:

Create the initial Django backend project structure for the OLX Sales Growth Agent.

Scope:

- create `backend/`
- initialize Django project
- configure SQLite database path
- create base app structure
- register initial apps
- add `.env.example`
- add basic README setup notes
- ensure project can run `manage.py check`

Out of scope:

- OLX API calls
- Telegram bot logic
- OpenAI logic
- Celery configuration
- domain models beyond minimal project bootstrapping

Acceptance criteria:

- `backend/manage.py` exists
- `backend/config/settings.py` exists
- SQLite is configured
- base app folders are present or intentionally planned
- `.env.example` includes required placeholder settings
- `python manage.py check` passes

Verification:

```bash
cd backend
python manage.py check
```

---

### TASK-002 — Add core domain models

Status: BACKLOG  
Priority: P0  
Type: backend/domain  
Owner: Codex  
Depends on: TASK-001

Goal:

Add core data models needed for OLX-connected workflow.

Scope:

Models:

- `Product`
- `ProductVariant`
- `OLXAccount`
- `Listing`
- `ListingVersion`
- `ListingMetricDaily`
- `Conversation`
- `Message`
- `ApprovalRequest`
- `AgentRun`

Acceptance criteria:

- models exist in appropriate apps
- migrations are created
- admin registration exists
- `__str__` methods are useful
- basic model creation tests exist
- migration check passes

Verification:

```bash
cd backend
python manage.py makemigrations --check --dry-run
python manage.py test
```

---

### TASK-003 — Add environment and settings structure

Status: BACKLOG  
Priority: P0  
Type: backend/config  
Owner: Codex  
Depends on: TASK-001

Goal:

Make configuration safe and environment-driven.

Scope:

- add environment variable loading
- add `.env.example`
- configure secrets from environment
- configure debug/allowed hosts
- configure SQLite database path from env with sensible default
- document required variables

Acceptance criteria:

- no real secrets in repo
- app works with default local `.env`
- missing critical env vars fail clearly where appropriate
- docs mention required variables

---

### TASK-004 — Add OLX OAuth skeleton

Status: BACKLOG  
Priority: P0  
Type: integration/olx  
Owner: Codex  
Depends on: TASK-002, TASK-003

Goal:

Create OLX OAuth integration skeleton.

Scope:

- create `apps/integrations/olx/auth.py`
- add OAuth URL generation
- add callback view/service skeleton
- add token refresh service
- persist token metadata in `OLXAccount`
- use environment variables for client ID/secret
- add tests for expiry/refresh decision logic

Out of scope:

- real production token exchange if credentials are unavailable
- listing import
- message polling

Acceptance criteria:

- OAuth service is isolated
- token refresh logic is centralized
- tests cover expiry detection
- no secrets committed
- errors are explicit

---

### TASK-005 — Add OLX API client foundation

Status: BACKLOG  
Priority: P0  
Type: integration/olx  
Owner: Codex  
Depends on: TASK-004

Goal:

Create a reusable OLX API client layer.

Scope:

- create `client.py`
- create `errors.py`
- create `rate_limiter.py`
- add base request handling
- add token injection
- add retry policy for safe errors
- add typed response wrappers where practical

Acceptance criteria:

- no raw OLX requests outside integration layer
- client is testable with mocked HTTP responses
- common error cases are represented
- rate limiter abstraction exists

---

### TASK-006 — Import OLX listings

Status: BACKLOG  
Priority: P0  
Type: integration/olx  
Owner: Codex  
Depends on: TASK-005

Goal:

Fetch current OLX adverts and persist/update local `Listing` records.

Scope:

- create `adverts.py`
- create listing sync service
- deduplicate by OLX advert ID
- persist title, status, price, category, city, URL where available
- create or update listing records
- log sync result

Acceptance criteria:

- sync is idempotent
- duplicate listings are not created
- failures are logged
- tests cover create/update paths

---

### TASK-007 — Poll OLX conversations and messages

Status: BACKLOG  
Priority: P0  
Type: integration/olx  
Owner: Codex  
Depends on: TASK-005, TASK-006

Goal:

Poll OLX threads/messages and persist new customer messages.

Scope:

- create `messages.py`
- import/update conversations
- import messages
- deduplicate by OLX message ID
- detect new customer messages
- create internal event or notification trigger

Acceptance criteria:

- duplicate messages are ignored
- conversations link to listing where possible
- new customer messages can be identified
- tests cover deduplication

---

### TASK-008 — Add Telegram bot foundation

Status: BACKLOG  
Priority: P0  
Type: integration/telegram  
Owner: Codex  
Depends on: TASK-002, TASK-003

Goal:

Create Telegram integration foundation.

Scope:

- create Telegram service layer
- configure bot token from environment
- configure authorized user IDs
- send simple notification message
- reject unauthorized callbacks/messages
- add tests for authorization logic

Acceptance criteria:

- no Telegram token in repo
- unauthorized users cannot control system
- service can send notification through an abstraction
- tests cover authorization

---

### TASK-009 — Add ApprovalRequest Telegram flow

Status: BACKLOG  
Priority: P0  
Type: approval/workflow  
Owner: Codex  
Depends on: TASK-008

Goal:

Allow Telegram approval/rejection of pending approval requests.

Scope:

- create approval message builder
- add inline button payload structure
- handle approve/reject callbacks
- update `ApprovalRequest` status
- store approved/rejected timestamp
- handle expired/missing requests

Acceptance criteria:

- pending approval can be approved/rejected
- status changes are persisted
- unauthorized callbacks are rejected
- tests cover approve/reject paths

---

### TASK-010 — Add daily report skeleton

Status: BACKLOG  
Priority: P1  
Type: reporting  
Owner: Codex  
Depends on: TASK-006, TASK-007, TASK-008

Goal:

Send a simple daily Telegram report from stored listings/messages/metrics.

Scope:

- create report service
- summarize listing counts
- summarize new messages
- summarize metric records if available
- handle missing metrics gracefully
- send to Telegram

Acceptance criteria:

- report does not crash when metrics are missing
- report text is concise and useful
- tests cover empty-data report

---

### TASK-011 — Add Celery and Celery Beat

Status: BACKLOG  
Priority: P1  
Type: backend/tasks  
Owner: Codex  
Depends on: TASK-003

Goal:

Add background task infrastructure.

Scope:

- configure Celery
- configure Redis broker
- add basic scheduled task placeholders
- add tasks for OLX sync/report skeletons only where dependencies exist
- document local run commands

Acceptance criteria:

- Celery app imports correctly
- worker can start locally
- beat schedule is configurable
- no heavy task concurrency assumptions with SQLite

---

### TASK-012 — Add deployment skeleton

Status: BACKLOG  
Priority: P1  
Type: devops  
Owner: Codex  
Depends on: TASK-011

Goal:

Add Docker Compose deployment skeleton.

Scope:

- backend Dockerfile
- docker-compose.yml
- Redis service
- SQLite persistent volume
- `.env.example`
- basic run instructions

Acceptance criteria:

- local containers can build
- SQLite database path is persistent
- secrets are not committed
- docs mention backup need

---

## 4. M2 — AI Drafting and Policy Layer

### TASK-013 — Add OpenAI integration service

Status: BACKLOG  
Priority: P1  
Type: integration/openai  
Owner: Codex  
Depends on: TASK-003

Goal:

Create isolated OpenAI service layer.

Acceptance criteria:

- API key from environment
- no direct OpenAI calls outside integration layer
- structured output helper exists
- errors are explicit
- tests use mocks

---

### TASK-014 — Add structured output schemas

Status: BACKLOG  
Priority: P1  
Type: ai/schemas  
Owner: Codex  
Depends on: TASK-013

Goal:

Define schemas for AI-generated business outputs.

Schemas:

- sales reply draft
- policy guard result
- listing draft
- hypothesis proposal
- creative request
- report summary

Acceptance criteria:

- schemas are typed
- invalid output is handled
- tests cover validation

---

### TASK-015 — Add AgentRun logging

Status: BACKLOG  
Priority: P1  
Type: ai/audit  
Owner: Codex  
Depends on: TASK-002, TASK-013

Goal:

Persist AI agent inputs/outputs and execution metadata.

Acceptance criteria:

- agent name, input summary, output, status stored
- sensitive data redaction strategy exists
- failures are logged

---

### TASK-016 — Implement Policy & Risk Guard Agent

Status: BACKLOG  
Priority: P1  
Type: agents/policy  
Owner: Codex  
Depends on: TASK-014, TASK-015

Goal:

Validate AI-generated replies and listing drafts against deterministic safety rules.

Acceptance criteria:

- catches unsupported product claims
- catches unsafe price/discount promises
- catches exact runtime claims without data
- catches waterproof/submersion claims
- returns structured risk result
- tests cover safe/unsafe examples

---

### TASK-017 — Implement Sales Chat Agent draft replies

Status: BACKLOG  
Priority: P1  
Type: agents/sales  
Owner: Codex  
Depends on: TASK-007, TASK-014, TASK-016

Goal:

Generate draft replies to customer messages.

Acceptance criteria:

- drafts only, no auto-send
- reply language follows customer language where practical
- reply passes Policy Guard before approval
- ApprovalRequest is created for non-trivial reply
- tests cover common customer questions

---

### TASK-018 — Connect draft replies to Telegram approval

Status: BACKLOG  
Priority: P1  
Type: workflow  
Owner: Codex  
Depends on: TASK-009, TASK-017

Goal:

Send generated customer reply drafts to Telegram for approval.

Acceptance criteria:

- approval message includes customer question and proposed reply
- approve action can execute send placeholder/service
- reject action stores rejection
- no reply bypasses approval

---

## 5. M3 — Metrics, Hypotheses, and Creative Requests

### TASK-021 — Store listing metric history

Status: BACKLOG  
Priority: P1  
Type: metrics  
Owner: Codex  
Depends on: TASK-006

Goal:

Collect and store daily listing metrics.

Acceptance criteria:

- views/phone/favorites/message counts stored when available
- idempotent by listing/date
- handles missing API fields gracefully

---

### TASK-022 — Implement Metrics Analyst Agent

Status: BACKLOG  
Priority: P2  
Type: agents/analytics  
Owner: Codex  
Depends on: TASK-021

Goal:

Analyze listing performance and identify weak points.

Acceptance criteria:

- calculates conversion metrics
- identifies low-performing listings
- returns structured analysis
- tests cover calculations

---

### TASK-023 — Implement Hypothesis Agent

Status: BACKLOG  
Priority: P2  
Type: agents/growth  
Owner: Codex  
Depends on: TASK-022

Goal:

Generate testable sales hypotheses.

Acceptance criteria:

- hypotheses include problem, change, target metric, success criterion
- no duplicate-spam recommendations
- risky actions require approval
- hypotheses stored

---

### TASK-024 — Implement Listing Creator Agent

Status: BACKLOG  
Priority: P2  
Type: agents/listing  
Owner: Codex  
Depends on: TASK-016, TASK-023

Goal:

Generate listing draft improvements.

Acceptance criteria:

- title/description drafts are generated
- output passes Policy Guard
- draft linked to hypothesis
- no auto-publish

---

### TASK-025 — Implement Creative Director Agent

Status: BACKLOG  
Priority: P2  
Type: agents/creative  
Owner: Codex  
Depends on: TASK-023

Goal:

Request specific photo/video assets for sales tests.

Acceptance criteria:

- creative requests include scene, angle, purpose, expected metric impact
- Telegram request can be sent
- uploaded media can be linked later

---

## 6. M4 — Semi-Autopilot

### TASK-029 — Implement safe intent classifier

Status: BACKLOG  
Priority: P2  
Type: agents/autopilot  
Owner: Codex  
Depends on: TASK-017

Goal:

Classify customer messages into safe/unsafe auto-reply intents.

Acceptance criteria:

- safe intents are deterministic/allowlisted
- uncertain messages escalate
- tests cover common safe and unsafe intents

---

### TASK-030 — Implement auto-reply allowlist

Status: BACKLOG  
Priority: P2  
Type: agents/autopilot  
Owner: Codex  
Depends on: TASK-029

Goal:

Allow automatic replies only for configured safe intents.

Acceptance criteria:

- disabled by default
- global kill switch exists
- every auto-reply is audited
- no discount/price-change promises outside rules

---

## 7. M5 — Dashboard

### TASK-036 — Initialize React dashboard

Status: BACKLOG  
Priority: P3  
Type: frontend  
Owner: Codex  
Depends on: M1, M2, M3

Goal:

Create React dashboard after backend workflows are functional.

Acceptance criteria:

- Vite + React + TypeScript initialized
- basic API client exists
- dashboard shell exists
- no disruption to backend workflow
