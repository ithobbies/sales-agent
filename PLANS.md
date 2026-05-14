---
name: context-update
description: Use this skill after completing a task to update TASKS.md, CONTEXT.md, and DECISIONS.md when needed.
---

# Context Update Skill

## Purpose

Keep repository-local project memory current.

## Workflow

1. Update task status in `TASKS.md`.
2. Add or update current implementation state in `CONTEXT.md`.
3. Add follow-up tasks if needed.
4. Update `DECISIONS.md` only if architecture changed.
5. Do not rewrite protected files unnecessarily.

## CONTEXT.md update fields

Include:

- last completed task
- date
- summary
- files changed
- checks run
- result
- new constraints
- follow-up tasks
- next recommended task

## Rule

Never rely on chat history as project memory. Project memory lives in repository files.
