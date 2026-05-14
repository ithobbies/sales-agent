---
name: security-review
description: Use this skill for OAuth, tokens, secrets, Telegram authorization, external APIs, customer data, auto-replies, and approval bypass risk.
---

# Security Review Skill

## Focus areas

- OAuth client secret handling
- access/refresh token storage
- token redaction in logs
- Telegram authorized user checks
- callback payload validation
- external API retry safety
- customer data in logs/tests
- approval bypass
- unsafe auto-replies
- price/discount rule bypass

## Mandatory checks

- No secrets in repository.
- `.env.example` contains placeholders only.
- Real `.env` is ignored.
- Tokens are never printed in logs.
- Unknown Telegram users cannot approve actions.
- Risky actions require `ApprovalRequest`.
- Tests do not call real external APIs.
