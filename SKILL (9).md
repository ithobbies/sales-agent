---
name: telegram-bot-task
description: Use this skill for Telegram notifications, approval buttons, callback handlers, authorization, and report delivery.
---

# Telegram Bot Task Skill

## Required location

Telegram code must live under:

```text
backend/apps/integrations/telegram/
```

## Workflow

1. Read approval-related rules in `AGENTS.md`.
2. Configure bot token through environment variables.
3. Configure authorized user IDs.
4. Create service methods for sending messages.
5. Add callback handling through safe payloads.
6. Link approval/rejection to `ApprovalRequest`.
7. Add tests for authorization.

## Security rules

- Reject unknown users.
- Never expose secrets in messages.
- Do not execute risky actions directly from callback without checking `ApprovalRequest`.
- Store approval result.
- Handle expired/missing requests.
