# AI Strategy One-Pager · Juno Automated Prioritization

## 1. Problem & Workflow

**The problem:** RocketShip's roadmap is ranked by recency and volume, not by evidence. In the weekly review the PM orders around 40 open items from memory and from whatever escalated in Slack most recently. When leadership challenges a rank, the PM cannot produce the reason inside the meeting, so the argument is settled by who pushed hardest and the decision is quietly reversed the week after.

**Prevention:** the single bad decision Juno prevents is moving an item up the backlog because someone senior posted forcefully, rather than because the cited evidence outweighs the alternatives.

## 2. Target Metrics

**Weekly prioritization time: reducing 2 hours to 30 minutes.** Derived from what the PM's job becomes. Today the two hours are spent assembling a ranking from memory. With Juno drafting it, the work is checking roughly 40 items against their cited sources, at about 30 seconds each to accept or override. That is 20 minutes of review plus discussion, so 30 minutes is the ceiling, not an aspiration.

**Reversals within one week: under 10%.** Derived from the backlog size. Ten percent of 40 items is 4, so the bar says at most four decisions get re-opened per quarter, roughly one a fortnight instead of several a week. Reversals are the honest measure because they count the times the first answer did not survive scrutiny.

**Leadership proof:** both are readable in 30 days from calendar time and the roadmap change log. No new instrumentation.

## 3. Autonomy Level

**Choice:** Copilot. Juno drafts the ranked backlog with reasoning and a source behind every line, and the PM approves before anything is published.

The value here is augmentation rather than automation. Juno is not removing the ranking decision, it is removing the blank page the PM currently starts from, which is why a person still approves before anything publishes.

**Explicitly avoiding:** Agent. The job splits cleanly in two. Drafting a ranking is reversible and costs a PM five minutes when it is wrong. Writing dates, moving people, or publishing to the live roadmap is a one-way door, and one bad autonomous call gives every sceptic permanent grounds to dismiss the system. The risk sits in the write action, not in the draft, so that is where the human stays.

## 4. Data & Model Approach

**Approach:** ground with RAG over the RocketShip corpus: `#escalations` and `#support`, the ROCKET Jira project, and the Product workspace in Notion. Every ranked item points at the thread or ticket that justifies it.

**Explicitly avoiding:** the fine-tuning shortcut. There is no labelled ranking data at RocketShip, priorities shift faster than labels could be refreshed, and weights cannot cite anything, so a fine-tuned model would still need retrieval underneath. It costs more, ships later, and hides its mistakes where retrieval shows them.

**When a bigger player copies this.** They will, and quickly, because the mechanism is not hard to build. What they cannot copy is the corpus: RocketShip's own escalations, tickets and decisions, plus the record of which past rankings held and which were reversed. A competitor starts that at zero and cannot buy it. The lead is not the retrieval pipeline, it is that ours cites our own arguments and theirs has none yet. It compounds every week we use it and decays if we stop, so the assets to defend are the corpus and the citation rule, not the model choice.

## 5. Risks & Mitigations

**Risk:** Juno launders opinion into evidence. Every rank arrives with a citation attached, so the output reads as objective even when the corpus itself is skewed toward whoever posts most often. One customer who escalates twelve times looks like twelve signals. This is worse than the problem we set out to fix, because leadership can no longer argue with a ranking that appears sourced, and the PM has handed away the judgement that used to catch it.

**Mitigation:** rank by distinct affected accounts, never by message or thread volume. One account escalating twelve times counts once. Every ranked item shows its denominator, how many distinct accounts and which segments, so a thin case is visible at a glance. Ship gate: no item reaches the top of the list on evidence from a single account.

## 6. V1 Scope

**In:** ranking the existing backlog with a source behind every item, showing the evidence denominator, and flagging where Slack escalations and Jira priorities disagree.

**Out,** and staying with the human PM:

1. Writing to the live roadmap. No dates, no sprint assignment, no status changes.
2. Anything involving headcount or hiring.
3. Customer-facing communication about why something was deprioritized.
