# DECISIONS.md

This file records architecture decisions. Codex must not change major architecture without adding/updating an ADR here.

---

## ADR-001 — Use Django modular monolith for MVP

Date: YYYY-MM-DD  
Status: Accepted

### Decision

Use Django + Django REST Framework as a modular monolith for the MVP.

### Context

The system needs:

- domain models
- admin interface
- API endpoints
- background jobs
- OLX integration
- Telegram integration
- approval workflows
- audit logs
- agent orchestration

### Rationale

A Django monolith is simpler to develop and operate than microservices at MVP stage. Django Admin is useful for internal configuration and debugging.

### Consequences

- Apps are separated by domain.
- Integrations are isolated under `backend/apps/integrations/`.
- Agents are logical modules, not independent services.
- Microservices are postponed until there is real scale pressure.

---

## ADR-002 — Use SQLite for MVP

Date: YYYY-MM-DD  
Status: Accepted

### Decision

Use SQLite for MVP instead of PostgreSQL.

### Context

The initial system is expected to serve one owner, one OLX account, a limited number of listings, and moderate message volume.

### Rationale

SQLite reduces setup and deployment complexity and is sufficient for a single-user MVP if write concurrency is controlled.

### Constraints

- Use WAL mode where practical.
- Avoid many parallel write-heavy workers.
- Keep ORM portable.
- Store media files outside SQLite.
- Back up the SQLite database file.

### Migration triggers

Move to PostgreSQL if:

- SQLite locking becomes frequent
- multiple active users are added
- several OLX accounts are connected
- message/metrics volume grows significantly
- analytics queries become heavy
- concurrent write load increases

---

## ADR-003 — OLX integration starts in M1

Date: YYYY-MM-DD  
Status: Accepted

### Decision

OLX integration is part of the first milestone.

### Context

The user does not want to manually enter metrics or monitor OLX messages. The project value depends on reducing routine from the start.

### Rationale

A manual-first MVP would not solve the main pain. The system must become OLX-connected early.

### Consequences

- OLX OAuth skeleton is an early task.
- Listing import is an early task.
- Message polling is an early task.
- Metrics collection is planned early.
- External API mocking is necessary for tests.

---

## ADR-004 — Telegram is the primary control interface

Date: YYYY-MM-DD  
Status: Accepted

### Decision

Use Telegram as the main day-to-day user interface.

### Context

The owner needs a lightweight control center for reports, approvals, customer message notifications, and photo/video requests.

### Rationale

Telegram is faster to use than a dashboard for daily actions and approvals.

### Consequences

- Telegram bot integration is part of M1.
- Dashboard is postponed.
- Approval buttons are implemented in Telegram.
- Unauthorized Telegram users must be blocked.

---

## ADR-005 — React dashboard is postponed

Date: YYYY-MM-DD  
Status: Accepted

### Decision

Do not build React dashboard until OLX-connected backend MVP is working.

### Context

The highest-value routine reduction comes from OLX sync, message polling, Telegram approvals, and reports.

### Rationale

Building dashboard too early risks delaying the core automation.

### Consequences

- M1 focuses on backend/integrations.
- Dashboard belongs to M5.
- Django Admin is used for internal configuration before dashboard exists.

---

## ADR-006 — ApprovalRequest gates risky actions

Date: YYYY-MM-DD  
Status: Accepted

### Decision

All risky actions must create and pass through `ApprovalRequest`.

### Risky actions

- publish listing
- edit live listing
- change price
- deactivate listing
- delete listing
- send non-trivial customer reply
- enable auto-replies
- mass update listings

### Rationale

The system must protect the OLX account, prevent hallucinated promises, and avoid unwanted business actions.

### Consequences

- Agents prepare drafts and proposals.
- Execution happens only after approval where required.
- Approval history is auditable.
- Telegram buttons update approval status.

---

## ADR-007 — External APIs are isolated behind integration layers

Date: YYYY-MM-DD  
Status: Accepted

### Decision

OLX, Telegram, and OpenAI access must be isolated under `backend/apps/integrations/`.

### Rationale

This avoids scattered external calls, improves testability, and reduces integration coupling.

### Consequences

- No raw OLX calls in agents/views/tasks.
- No raw Telegram calls in agents/views/tasks.
- No raw OpenAI calls in agents/views/tasks.
- External API tests use mocks/fakes.

---

## ADR-008 — AI starts in draft mode

Date: YYYY-MM-DD  
Status: Accepted

### Decision

AI-generated replies and listing changes start as drafts.

### Rationale

The system needs real-world validation before automatic customer communication or listing updates.

### Consequences

- Sales Chat Agent creates draft replies.
- Policy Guard validates drafts.
- Telegram approval is required.
- Auto-replies can be added later only with allowlists and kill switch.

---

## ADR-009 — Structured outputs for business-critical AI results

Date: YYYY-MM-DD  
Status: Accepted

### Decision

Use structured outputs for AI-generated business objects.

### Examples

- sales reply draft
- policy guard result
- listing draft
- hypothesis proposal
- creative request
- report summary

### Rationale

Free-form text is harder to validate and audit. Structured outputs make validation, persistence, and approval safer.

### Consequences

- Define schemas.
- Validate AI outputs.
- Reject invalid outputs.
- Persist important outputs in `AgentRun`.

---

## ADR-010 — Task tracker lives in repository first

Date: YYYY-MM-DD  
Status: Accepted

### Decision

Use `TASKS.md` as the initial task tracker.

### Rationale

Codex can always read the repository-local tracker. This keeps work scoped and prevents context loss.

### Consequences

- Codex updates `TASKS.md` after tasks.
- External tools like GitHub Issues or Linear can be added later.
- Repository files remain the source of truth.
