# AI Strategy One-Pager · Juno Automated Prioritization

## 1. Problem & Workflow

RocketShip's roadmap is ranked by recency and volume, not by evidence. In the weekly review the PM orders around 40 open items from memory and from whatever escalated in Slack most recently. When leadership challenges a rank, the PM cannot produce the reason inside the meeting, so the argument is settled by who pushed hardest and the decision is quietly reversed the week after.

The single bad decision Juno prevents: moving an item up the backlog because someone senior posted forcefully, rather than because the cited evidence outweighs the alternatives.

## 2. Target Metrics

Average weekly prioritization time falls from 2 hours to 30 minutes.

The number that makes leadership protect this: the share of ranking decisions reversed within one week drops under 10%. Reversals are the honest measure, because they count the times the first answer did not survive contact with scrutiny.

Both are readable in the first 30 days from calendar time and the roadmap change log, with no new instrumentation to build.

## 3. Autonomy Level

Copilot. Juno drafts the ranked backlog with reasoning and a source behind every line, and the PM approves before anything is published.

Explicitly avoiding Agent. The job splits cleanly in two. Drafting a ranking is reversible and costs a PM five minutes when it is wrong. Writing dates, moving people, or publishing to the live roadmap is a one-way door, and one bad autonomous call gives every sceptic permanent grounds to dismiss the system. The risk sits in the write action, not in the draft, so that is where the human stays.

## 4. Data & Model Approach

Ground with RAG over the RocketShip corpus: `#escalations` and `#support`, the ROCKET Jira project, and the Product workspace in Notion. Every ranked item points at the thread or ticket that justifies it.

Explicitly not taking the fine-tuning shortcut. There is no labelled ranking data at RocketShip, priorities shift faster than labels could be refreshed, and weights cannot cite anything, so a fine-tuned model would still need the retrieval layer underneath it. It would cost more, ship later, and hide its mistakes where retrieval shows them.

## 5. Risks & Mitigations

The risk: Juno launders opinion into evidence. Every rank arrives with a citation attached, so the output reads as objective even when the corpus itself is skewed toward whoever posts most often. One customer who escalates twelve times looks like twelve signals. This is worse than the problem we set out to fix, because leadership can no longer argue with a ranking that appears sourced, and the PM has handed away the judgement that used to catch it.

The mitigation: rank by distinct affected accounts, never by message or thread volume. One account escalating twelve times counts once. Every ranked item shows its denominator, how many distinct accounts and which segments, so a thin case is visible at a glance. Ship gate: no item reaches the top of the list on evidence from a single account.

## 6. V1 Scope

In: ranking the existing backlog with a source behind every item, showing the evidence denominator, and flagging where Slack escalations and Jira priorities disagree.

Out, and staying with the human PM:

1. Writing to the live roadmap. No dates, no sprint assignment, no status changes.
2. Anything involving headcount or hiring.
3. Customer-facing communication about why something was deprioritised.
