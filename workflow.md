---
name: code-review
description: Use this skill before marking any implementation task done, especially after code changes, migrations, integrations, or agent behavior changes.
---

# Code Review Skill

## Review checklist

Check for:

- unrelated changes
- broad refactors outside scope
- missing tests
- broken imports
- missing migrations
- unsafe exception handling
- duplicated logic
- architecture boundary violations
- direct OLX calls outside OLX integration layer
- direct Telegram calls outside Telegram integration layer
- direct OpenAI calls outside OpenAI integration layer
- missing ApprovalRequest for risky actions
- missing context/task updates
- secrets accidentally committed

## Output format

```text
Review result:
- Scope respected: yes/no
- Tests added/updated: yes/no
- Checks run: ...
- Blockers: ...
- Risks: ...
- Follow-up tasks: ...
```
