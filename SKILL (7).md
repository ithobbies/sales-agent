---
name: olx-integration-task
description: Use this skill for OLX OAuth, API clients, listings, messages, metrics, polling, rate limiting, and OLX error handling.
---

# OLX Integration Task Skill

## Required location

All OLX code must live under:

```text
backend/apps/integrations/olx/
```

## Workflow

1. Read `DECISIONS.md` for OLX-related ADRs.
2. Do not invent OLX endpoints.
3. Create small service methods around documented or configurable API calls.
4. Keep HTTP logic in `client.py`.
5. Keep OAuth/token logic in `auth.py`.
6. Keep adverts/listings logic in `adverts.py`.
7. Keep messages logic in `messages.py`.
8. Keep metrics logic in `statistics.py`.
9. Mock all external calls in tests.
10. Add idempotency and deduplication.

## Safety rules

- No direct OLX API calls from agents/views/tasks.
- No secrets in repo.
- No publishing/editing/deleting without `ApprovalRequest`.
- Polling interval must be configurable.
- Log errors without leaking tokens.
