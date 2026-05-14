# CONTEXT.md

## 1. Current Project State

Project: `led-sales-agent`

Goal:

Create an OLX-first AI sales growth system for selling USB LED COB strips, minimizing manual routine for the owner.

The system should eventually:

- connect to OLX
- read listings
- read messages
- collect metrics
- generate safe sales replies
- propose growth hypotheses
- create listing drafts
- request needed photo/video
- send reports and approvals in Telegram
- keep risky actions behind approval gates

---

## 2. Current Architecture

Architecture status: planned, not implemented yet.

Current decisions:

- Django modular monolith
- SQLite for MVP
- OLX integration from first milestone
- Telegram as primary control interface
- React dashboard postponed until backend MVP works
- one orchestrator + specialized logical agents
- risky actions handled through `ApprovalRequest`
- external APIs isolated under `backend/apps/integrations/`
- agents implemented under `backend/apps/agents/`

---

## 3. Current Implementation Status

Last updated: YYYY-MM-DD

Completed:

- Project workflow files prepared:
  - `AGENTS.md`
  - `PLANS.md`
  - `TASKS.md`
  - `CONTEXT.md`
  - `DECISIONS.md`
  - `.codex/config.toml`
  - `.codex/agents/*.toml`
  - `.agents/skills/**/SKILL.md`

In progress:

- none

Next task:

- `TASK-001 — Initialize Django project`

---

## 4. Current Repository Layout Target

Target structure:

```text
led-sales-agent/
├── AGENTS.md
├── PLANS.md
├── TASKS.md
├── CONTEXT.md
├── DECISIONS.md
├── README.md
├── backend/
│   ├── manage.py
│   ├── config/
│   └── apps/
│       ├── core/
│       ├── products/
│       ├── listings/
│       ├── conversations/
│       ├── metrics/
│       ├── hypotheses/
│       ├── approvals/
│       ├── agents/
│       └── integrations/
│           ├── olx/
│           ├── telegram/
│           └── openai/
├── docs/
├── .codex/
│   ├── config.toml
│   └── agents/
└── .agents/
    └── skills/
```

Actual structure should be updated here after implementation begins.

---

## 5. Product Facts

Known product:

- USB LED COB strip
- 5 meters
- 5V USB
- works from powerbank / USB charger / laptop / 5V USB source
- cold white light
- switch on cable
- silicone covering
- moisture resistant
- adhesive backing
- useful for blackouts, room, kitchen, corridor, garage, cottage, tent, work area
- current known price: 240 UAH

Unknown product facts that must not be invented:

- exact wattage
- exact current draw
- exact runtime from 10,000 mAh / 20,000 mAh powerbanks
- warranty terms
- exact stock count
- exact delivery options
- discount policy
- actual margin
- exact OLX category/attribute IDs

---

## 6. Business Rules

Initial rules:

- price is 240 UAH unless changed by owner
- do not sell below minimum price until configured
- do not promise exact powerbank runtime without data
- do not promise full waterproofing/submersion
- do not publish duplicate or near-duplicate listings
- do not auto-send non-trivial customer replies at MVP stage
- do not auto-publish listings at MVP stage
- do not auto-change prices

---

## 7. Integration Assumptions

OLX:

- API access requires developer setup and OAuth credentials.
- Actual API behavior must be verified in implementation.
- Do not invent endpoints.
- Use integration service abstractions and mocked tests.

Telegram:

- Telegram is the control center.
- Authorized user ID(s) must be configured.
- Approval flows use callback buttons.

OpenAI:

- OpenAI calls must go through integration layer.
- Use structured outputs for business-critical objects.
- Persist significant agent outputs in `AgentRun`.

---

## 8. Current Risks

Technical risks:

- OLX API access approval may take time.
- OLX endpoints/permissions may differ from expectations.
- SQLite write concurrency may become limiting later.
- Telegram callback security must be implemented correctly.
- AI must not hallucinate product claims or unsafe promises.

Mitigation:

- isolate OLX client
- mock external APIs in tests
- use ApprovalRequest for risky actions
- keep AI in draft mode first
- use Policy Guard
- update this context after every task

---

## 9. Context Update Template

After each completed task, append/update this section:

```text
Last completed task:
- Task ID:
- Date:
- Summary:
- Files changed:
- Checks run:
- Result:
- New constraints:
- Follow-up tasks:
- Next recommended task:
```

---

## 10. Last Completed Task

Task ID: none  
Date: none  
Summary: implementation has not started yet.  
Checks run: none.  
Next recommended task: `TASK-001`.
