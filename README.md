![Juno PM](juno-pm-cover.jpg)

# Juno PM · AI Copilot for RocketShip's Product Org

> Juno PM turns RocketShip's flood of escalations, tickets and customer calls into a short, ranked, sourced list of what matters, so a product manager can decide quickly and defend the call afterwards.

_Antje Barth · AI Product Management Certification · Aug 2026 cohort_

Repo: https://github.com/antje/product-school-ai-product-management

This repo is my final project for the **AI Product Management Certification**. Each module's artifact lives in its own folder; this README is the dashboard and the pitch.

---

## Status

| Module | Deliverables | Done |
|---|---|---|
| M1 · Prompting | System prompt, Lovable prototype | Yes |
| M2 · Strategy | Decision matrix, strategy one-pager | Yes |
| M3 · RAG / AI PRD | AI PRD | Yes |
| M4 · AI-Native UX | User flow, trust gaps | Yes |
| M5 · Agentic Workflows | AWSpec, control panel | Yes |
| M6 · Evals & Guardrails | Eval stack, human rubric | Yes |

---

## Module artifacts

### M1 · Prompting
- **System prompt**: [`01-prompting/system-prompt.md`](01-prompting/system-prompt.md)
- **Lovable prototype**: [`01-prompting/lovable-prototype.md`](01-prompting/lovable-prototype.md) · live at [ai-pm-synthesis.lovable.app](https://ai-pm-synthesis.lovable.app)

### M2 · Strategy
- **Decision matrix**: [`02-strategy/decision-matrix.md`](02-strategy/decision-matrix.md)
- **AI Strategy one-pager**: [`02-strategy/strategy-one-pager.md`](02-strategy/strategy-one-pager.md)

### M3 · RAG / AI PRD
- **AI PRD**: [`03-rag-prd/prd.md`](03-rag-prd/prd.md)

### M4 · AI-Native UX
- **AI user flow**: [`04-ai-ux/user-flow.md`](04-ai-ux/user-flow.md) · [flow diagram](04-ai-ux/user-flow.png)
- **Trust-gap mitigations**: [`04-ai-ux/trust-gaps.md`](04-ai-ux/trust-gaps.md)
- **Prototype screenshots**: quality mode ranks on request quality alone; strategy mode declines with a cited exclusion clause.

| Quality mode | Strategy mode |
|---|---|
| ![Juno ranking without the strategy document](04-ai-ux/screenshots/juno-quality-mode.png) | ![Juno ranking grounded in the strategy document](04-ai-ux/screenshots/juno-strategy-mode.png) |

### M5 · Agentic Workflows
- **Agent Workflow Spec (AWSpec)**: [`05-agentic-workflows/awspec.md`](05-agentic-workflows/awspec.md) · [workflow diagram](05-agentic-workflows/awspec.png)
- **Agent Control Panel**: [`05-agentic-workflows/agent-control-panel.md`](05-agentic-workflows/agent-control-panel.md) · [control panel diagram](05-agentic-workflows/agent-control-panel.png)

### M6 · Evals & Guardrails
- **Eval stack**: [`06-evals/eval-stack.md`](06-evals/eval-stack.md) · [stack diagram](06-evals/eval-stack.png)
- **Human evaluation rubric**: [`06-evals/human-rubric.md`](06-evals/human-rubric.md)

---

## PM Execution Plan

### Where Juno is today
- Eleven artifacts specced and committed, M1 through M6.
- The prototype is live at [ai-pm-synthesis.lovable.app](https://ai-pm-synthesis.lovable.app) and has been grounded in the strategy document since the M3 rebuild. It is a test harness for the reasoning, not the shipping surface.
- The agent is a specification. No tool call has run against live Slack, Jira or Notion.
- Golden set: 40 cases composed and allocated across handoff conditions and routing paths. None written yet.
- Human rubric: five dimensions, 25 anchors, pass bars set. No calibration round run, graders not staffed.
- Nothing is wired to CI.

### What ships next (next 2 sprints)
- **Sprint 1, make the checks real.** Build clause fidelity and source resolution as blocking CI checks. Write the first 20 golden set cases, the 9 handoffs plus 11 drafted shortlists. Run one calibration round with 2 graders over 12 items and fix whichever anchors they read differently.
- **Sprint 2, read-only in production.** Juno posts the shortlist into `#escalations` behind a flag and stages nothing. Measure trigger precision, the share of fired runs the PM says was worth firing. No write scope is granted until that number holds, because a wrong write costs more than a wrong suggestion.

### What I watch (dashboards)
- **Daily:** handoff rate, run discard rate (the thread moved on mid-run), missed refusal triggers.
- **Weekly:** override rate, median time to approval, staged batch expiry, rubric mean per dimension.
- **Per release:** clause fidelity, out-of-scope source ids, golden set regressions, LLM-judge agreement with the last human round.
- **Monthly:** tokens per month against the 4.7M ceiling, and cost per run against the two hours a week Juno is meant to give back.

### Red lines (what blocks shipping, numbers not feelings)
- Clause fidelity below 100% on the full golden set.
- More than 0 `source_id` values resolving outside `#escalations`, `#support`, ROCKET or the Product workspace.
- Any item scored 1 on access safety in the latest human round.
- Any item scored 1 on handoff correctness in the latest human round.
- More than 0 golden set cases newly failing against the previous release.
- Override rate below 10% for a full week. That one is not a quality failure, it is the checkpoint quietly ceasing to exist, and it is the reason the other five stay meaningful.

### Governance
- **Compliance.** Contracts, revenue figures, direct messages and private channels are excluded when the index is built, not filtered after retrieval, so data Juno should not see never enters the corpus. Retrieval is scoped to the requesting team and the last 90 days.
- **Safety.** Every write is staged and executes only on approval. Threads touching contracts, legal, security or a customer's commercial terms cannot be drafted at all until a human approves.
- **Reliability.** Provider outage fails the run closed, with no silent downgrade to a smaller model. Six tool calls, 45 seconds wall clock, abort after two tool errors. A manual kill switch is held by the PM who owns `#escalations` and by workspace admins.
- **Reputation.** The ranking rules are versioned with a named owner and stamped on every shortlist, so any past ranking can be explained against the rules in force when it was made.

---

## Build Insights

- **Friction point.** The first prototype looked finished and had no model behind it. I ran the same transcript twice in two separate browser sessions and got byte-identical output, which is not something a model does. After that I stopped trusting what the screen implied and checked what the thing actually did.
- **Key learning.** Every guardrail I wrote ended in the same place: the PM approves. It took until the eval stack to notice that nothing was watching whether the PM still read what they were approving. A checkpoint that degrades quietly is worse than no checkpoint, because it keeps looking like a control long after it has stopped being one.
- **Aha moment.** Quality mode could rank. Only strategy mode could decline. Grounding did not make the ranking smarter, it made refusal possible, and refusing is the thing a PM cannot do from memory in a meeting.

---

## Repo structure

```
product-school-ai-product-management/
├── README.md                          ← this dashboard + pitch
├── juno-pm-cover.jpg                  ← title image
├── 01-prompting/
│   ├── system-prompt.md               ← M1: Juno's system prompt
│   └── lovable-prototype.md           ← M1: prototype link + debrief
├── 02-strategy/
│   ├── decision-matrix.md             ← M2: build / buy / fine-tune / partner call
│   └── strategy-one-pager.md          ← M2: AI strategy one-pager
├── 03-rag-prd/
│   └── prd.md                         ← M3: AI PRD with retrieval requirements
├── 04-ai-ux/
│   ├── user-flow.md                   ← M4: AI-native user flow
│   ├── user-flow.png                  ← M4: flow diagram
│   ├── trust-gaps.md                  ← M4: trust-gap mitigations
│   └── screenshots/                   ← M4: prototype, quality vs strategy mode
├── 05-agentic-workflows/
│   ├── awspec.md                      ← M5: Agent Workflow Spec
│   ├── awspec.png                      ← M5: workflow diagram
│   ├── agent-control-panel.md          ← M5: Agent Control Panel
│   └── agent-control-panel.png         ← M5: control panel diagram
└── 06-evals/
    ├── eval-stack.md                  ← M6: layered eval stack
    ├── eval-stack.png                  ← M6: stack diagram
    └── human-rubric.md                ← M6: human evaluation rubric
```

---

_Certification submission, AI Product Management Certification._
