# AGENTS.md

## 1. Project Identity

Repository: `led-sales-agent`

Purpose: build an OLX-first AI sales growth system for selling USB LED COB strips.

The system must integrate with OLX from the first milestone, read listings and customer messages, collect listing metrics, generate sales replies, prepare listing drafts, propose growth hypotheses, request photo/video assets, and route risky actions through human approval.

This repository is not a generic chatbot project. It is a business automation system with strict task discipline, auditability, and approval gates.

---

## 2. Core Product Context

Product currently sold:

- USB LED COB strip
- 5 meters
- 5V USB power
- works from powerbank, USB charger, laptop, or 5V USB source
- cold white light
- switch on cable
- silicone covering / moisture resistant
- adhesive backing
- main customer pain: lighting during blackouts
- current known price: 240 UAH per unit

Important product safety rules:

- Do not promise exact runtime from a powerbank without power consumption and powerbank capacity.
- Do not promise full waterproofing or submersion.
- Do not claim unsupported certification, warranty, brightness, battery runtime, or delivery options.
- Do not promise discounts outside configured pricing rules.
- Do not request customer bank card data.

---

## 3. Current Technology Decisions

Backend:

- Python 3.12+
- Django
- Django REST Framework
- SQLite for MVP
- Redis
- Celery + Celery Beat

Integrations:

- OLX Partner API
- Telegram Bot API
- OpenAI API / Agents SDK

AI architecture:

- one Orchestrator Agent
- specialized logical agents
- structured outputs for business-critical AI results
- all agent actions persisted in audit models where relevant

Frontend:

- React + TypeScript + Vite later
- React dashboard must not be started before M1 backend workflow is complete unless the user explicitly changes this decision

Deployment:

- Docker Compose
- VPS-ready
- SQLite file stored in persistent volume
- secrets through environment variables only

---

## 4. Agent Architecture

The application must be implemented first as a modular Django monolith.

Logical agents:

1. Orchestrator Agent
2. Data Collector Agent
3. Metrics Analyst Agent
4. Hypothesis Agent
5. Listing Creator Agent
6. Creative Director Agent
7. Sales Chat Agent
8. Policy & Risk Guard Agent
9. Report Agent

Do not prematurely split agents into microservices.

Agents must be implemented as maintainable Python modules/classes under:

```text
backend/apps/agents/
```

External API access must be isolated under:

```text
backend/apps/integrations/
```

---

## 5. Source of Truth

Codex must not rely on chat memory as the source of truth.

Before every task, read:

1. `AGENTS.md`
2. `CONTEXT.md`
3. `PLANS.md`
4. `TASKS.md`
5. `DECISIONS.md`

If there is a conflict:

1. User's latest explicit instruction wins.
2. `DECISIONS.md` wins for architectural decisions.
3. `TASKS.md` wins for task scope.
4. `CONTEXT.md` wins for current implementation state.
5. `AGENTS.md` wins for workflow rules.

If a required fact is missing, do not invent it. Either inspect the codebase, use official documentation, create a clearly marked TODO/stub, or ask for clarification if the task cannot proceed safely.

---

## 6. Protected Files Policy

Protected files:

- `AGENTS.md`
- `PLANS.md`
- `TASKS.md`
- `CONTEXT.md`
- `DECISIONS.md`
- `.codex/config.toml`
- `.codex/agents/*.toml`
- `.agents/skills/**/SKILL.md`

Codex may update:

- `TASKS.md` after task status changes
- `CONTEXT.md` after every completed task
- `DECISIONS.md` only when an architectural decision is explicitly made or changed
- `PLANS.md` only when the roadmap changes
- `AGENTS.md` only when the user explicitly asks to change agent rules

Never silently rewrite protected files as part of normal implementation.

---

## 7. Development Workflow

Codex must work on one task at a time.

### Before implementation

1. Identify the exact task ID from `TASKS.md`.
2. Confirm task status is `READY`.
3. Read acceptance criteria.
4. Inspect relevant files.
5. Produce a short execution plan.
6. Identify expected changed files.
7. Do not start coding until the scope is clear.

### During implementation

1. Make small, reviewable changes.
2. Stay inside task scope.
3. Do not refactor unrelated code.
4. Do not rename or move files unless the task explicitly requires it.
5. Keep domain logic separate from integration clients.
6. Add or update tests for new behavior.
7. Handle external API failures explicitly.
8. Avoid broad architectural changes.

### After implementation

1. Run relevant checks.
2. Review the diff.
3. Fix issues discovered by tests or self-review.
4. Update `TASKS.md`.
5. Update `CONTEXT.md`.
6. Update `DECISIONS.md` only if architecture changed.
7. Report what was changed, checks run, risks, and next task.

---

## 8. Definition of Done

A task is done only when all of the following are true:

- scope is implemented
- acceptance criteria are met
- relevant tests are added or updated
- relevant checks pass, or failures/blockers are documented
- no unrelated changes are included
- diff has been self-reviewed
- `TASKS.md` is updated
- `CONTEXT.md` is updated
- `DECISIONS.md` is updated if architectural decisions changed
- any new follow-up work is added to `TASKS.md`

Do not mark a task as `DONE` if checks were not run unless there is a clear documented reason.

---

## 9. Verification Commands

Use these commands when available:

```bash
cd backend
python manage.py check
python manage.py makemigrations --check --dry-run
python manage.py test
```

If pytest is configured:

```bash
pytest
```

If linting/formatting is configured:

```bash
ruff check .
ruff format --check .
```

If typing is configured:

```bash
mypy .
```

Do not claim that tests pass unless they actually ran.

---

## 10. SQLite Rules

SQLite is used for MVP.

Rules:

- Keep the database path stable and documented.
- Store SQLite database in a persistent volume for deployment.
- Use WAL mode where practical.
- Avoid many concurrent writers.
- Avoid unnecessary Celery concurrency for write-heavy tasks.
- Store uploaded media files on disk or object storage, not inside SQLite.
- Keep ORM usage portable for future PostgreSQL migration.
- Do not introduce SQLite-specific raw SQL unless necessary and documented.

Migration trigger to PostgreSQL:

- frequent `database is locked` issues
- multiple users actively using dashboard
- high write volume
- multiple OLX accounts
- large message/metric history
- heavy analytics requiring more concurrency

---

## 11. OLX Integration Rules

All OLX logic must be isolated under:

```text
backend/apps/integrations/olx/
```

Expected structure:

```text
auth.py
client.py
adverts.py
messages.py
statistics.py
categories.py
media.py
sync.py
rate_limiter.py
errors.py
```

Rules:

- Do not call OLX API directly from views, agents, or Celery tasks.
- Use integration service methods.
- Read credentials from environment variables only.
- Persist OAuth token metadata through secure models/services.
- Token refresh must be centralized.
- Add retry handling where safe.
- Add rate limiting.
- Deduplicate imported listings, threads, and messages.
- Polling intervals must be configurable.
- Destructive or publishing actions must create `ApprovalRequest`.

Risky OLX actions requiring approval:

- publish listing
- update live listing
- change price
- deactivate listing
- delete listing
- send non-trivial customer reply
- enable auto-replies
- mass update listings

---

## 12. Telegram Integration Rules

Telegram is the primary control interface.

Use Telegram for:

- daily reports
- approval requests
- new customer message notifications
- photo/video requests
- task status summaries
- urgent errors requiring human attention

Rules:

- Do not allow unknown Telegram users to control the system.
- Validate authorized user IDs.
- Use callback buttons for approval flows.
- Every approval callback must update an `ApprovalRequest`.
- Telegram integration code must live under:

```text
backend/apps/integrations/telegram/
```

---

## 13. OpenAI / AI Rules

OpenAI integration code must live under:

```text
backend/apps/integrations/openai/
```

Rules:

- Use structured outputs for business-critical results.
- Persist important agent runs in `AgentRun`.
- Keep prompts versioned when they materially affect behavior.
- Do not parse free text where a schema is required.
- Policy Guard must validate AI-generated customer replies and listing drafts.
- AI output is advisory until it passes deterministic checks and approval rules.

AI must not:

- invent endpoints
- invent product specs
- invent customer commitments
- invent delivery methods
- send messages without configured approval mode
- change prices below configured minimum
- mark tasks complete without verification

---

## 14. ApprovalRequest Rules

Risky business actions must go through `ApprovalRequest`.

Recommended statuses:

- `pending`
- `approved`
- `rejected`
- `expired`
- `executed`
- `failed`

Every approval request should store:

- action type
- proposed payload
- proposed by agent/service
- risk level
- policy guard result
- created timestamp
- approved/rejected timestamp
- executor result
- error details if failed

No risky action may bypass this model.

---

## 15. Testing Rules

Add tests close to the changed behavior.

Test priorities:

1. core domain models
2. approval workflow
3. OLX token refresh and deduplication
4. Telegram authorization
5. Policy Guard checks
6. agent structured output parsing
7. Celery task idempotency

External API tests must use mocks/fakes. Do not call real OLX, Telegram, or OpenAI APIs in normal tests.

---

## 16. Security Rules

Never commit:

- API keys
- OAuth client secrets
- access tokens
- refresh tokens
- Telegram bot token
- customer personal data fixtures
- production SQLite database
- `.env` with real values

Use `.env.example` for required variables.

Customer data must be handled with care:

- store only what is needed
- avoid leaking customer messages into logs
- redact tokens and secrets in exceptions
- do not expose internal IDs to customers

---

## 17. Git Workflow

One branch per task:

```text
task/TASK-001-init-django
task/TASK-002-core-models
task/TASK-003-olx-oauth
```

Commit style:

```text
TASK-001: initialize Django backend
TASK-002: add core domain models
TASK-003: add OLX OAuth skeleton
```

Pull request rules:

- one PR = one task
- PR title starts with task ID
- no mixed task scopes
- include tests/checks run
- include known risks
- include follow-up tasks if needed

---

## 18. Subagent Usage

Use subagents for complex or high-risk tasks.

Recommended pattern:

- Explorer: read-only codebase analysis
- Implementer: scoped implementation
- Reviewer: diff review
- Security Reviewer: OAuth, tokens, customer data, permissions
- Test Runner: test execution and repair loop

For small tasks, one implementer is enough.

For OLX OAuth, Telegram auth, auto-replies, or security-sensitive changes, use Reviewer and Security Reviewer before marking done.

---

## 19. Anti-Hallucination Rules

Evidence before implementation:

- inspect actual files before editing
- do not assume module names
- do not assume API endpoints
- do not assume model fields
- do not assume tests pass
- do not assume configuration exists

If something is unknown:

- search the repository
- read official docs when available
- create a small abstraction with TODO if external details are blocked
- document uncertainty in `CONTEXT.md` or task notes

Never write fake certainty.

---

## 20. Completion Report Format

When reporting task completion, use this format:

```text
Task: TASK-XXX — Title

Summary:
- ...

Files changed:
- ...

Checks run:
- ...

Result:
- ...

Risks / limitations:
- ...

Context updates:
- ...

Next recommended task:
- TASK-YYY
```

Keep reports concise but factual.
