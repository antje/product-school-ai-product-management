# AI Solution Decision Matrix · Juno

> Module 2 · Strategy. The technical approach call for Juno's automated prioritization engine.

## The decision

RocketShip's roadmap is ranked by recency and volume, not by evidence. In the weekly review the PM orders around 40 open items from memory and from whatever escalated in Slack most recently. When leadership challenges a rank, the PM cannot produce the reason inside the meeting, so the loudest recent thread wins and the decision is quietly reversed the week after. Module 1 defined how Juno reads a pile of raw input: cite every claim, group by root cause, name what actually broke. Ranking those findings against each other is the next bottleneck.

Framing the options honestly: all three rent a base model. Nobody at RocketShip is training one, and we have innovation budget with no headcount. So the real decision is **where we spend engineering effort**, and each option is a different answer:

- **Build.** Spend it on the evidence layer. We own the connectors into `#escalations` and `#support`, the ROCKET Jira project and the Product workspace in Notion, plus the retrieval and the rule that every rank must point at a source.
- **Buy / API.** Spend nothing beyond prompting. A general model ranks the backlog from what it is given, ungrounded.
- **Fine-tune.** Spend it on labelled ranking data, baking last quarter's priorities into weights.

Why now: the corpus already exists, the extraction rules are already written, and prioritization is where the PM's credibility is currently being spent.

**The capability this needs is retrieval, not reasoning horsepower.** The evidence exists and cannot be produced on demand, which makes this a retrieval and citation problem. RAG is therefore the minimum that solves it. We are explicitly not going agentic. Ranking asks Juno to retrieve, cluster and order, not to call tools and act, and every tool Juno could call is a tool that can write. Orchestration would add capability the job does not need and failure modes we would then have to guard.

## Options scored

Scored 1 to 5, where 5 is better for the PM: cheap, fast, high control, strong moat, low risk. Score is the average.

| Option | Cost | Speed | Control | Moat | Risk | Score |
|---|---|---|---|---|---|---|
| Build | 3 | 3 | 5 | 5 | 4 | 4.0 |
| Buy / API | 5 | 5 | 2 | 1 | 2 | 3.0 |
| Fine-tune | 2 | 2 | 4 | 2 | 2 | 2.4 |

The axis that decides this is not the one the scores make loudest. Cost and Speed favour Buy / API by a wide margin, and on a normal feature that would settle it. It does not settle this one, because the job Juno is being hired for is defensibility. A ranking the PM cannot trace is worthless in the meeting where it matters, however good it is. That pushes the weight onto Control and Moat.

**Build** loses on Cost and Speed. Stated assumption, not a quote from engineering: connectors into three systems plus a vector store is weeks of work rather than days, on the existing team with no new headcount. If it turns out to be months, Cost and Speed both drop a point and the gap to Buy narrows, so this is the first estimate to confirm. It takes Control because we decide what is retrievable and what a citation must point at, and Moat because the corpus and the citation rule compound while a model does not. Risk is low for the same reason. Grounded output is checkable, so a bad rank is visible in seconds rather than discovered a week later.

**Buy / API** is genuinely tempting on a budget with no headcount. It fails on provenance. An ungrounded model ranking a backlog produces confident output with nothing behind it, which is the loudest-voice problem again in better prose. Module 1 already showed us this exact failure: the prototype wrote a fluent brief that never named what was broken, and it was convincing precisely because it was fluent.

**Fine-tune** scores worst. There is no labelled ranking data at RocketShip, priorities shift faster than labels could be refreshed, and weights cannot cite anything, so it would still need the retrieval layer underneath. It is the Build cost plus a labelling programme, and it hides its mistakes instead of showing them.

## Recommendation

Build the evidence layer and rent the model.

The business reason: our durable asset is the corpus and the citation rule, not the weights. Model quality is a commodity that improves for free as providers compete, and we can swap providers as long as retrieval stays ours. What no vendor ships is the ability to trace a ranking decision back to the thread or ticket that justifies it, and that is the entire product. Buying is cheaper on day one and worth nothing on the day someone challenges a rank.

Autonomy stays at Copilot. Juno drafts the ranked backlog with the evidence attached, and the PM approves before anything is published. A wrong draft costs a PM five minutes. Writing dates, moving headcount or publishing to the live roadmap is a one-way door, and Juno does not touch those at any autonomy level. The risk sits in the write action, not the draft.

Measured in 30 days, without new instrumentation. **Prioritization time, reducing 2 hours to 30 minutes:** the two hours today go on assembling a ranking from memory; with Juno drafting it the work becomes checking about 40 items against their cited sources at roughly 30 seconds each, so 20 minutes of review plus discussion. **Reversals under 10%:** ten percent of a 40 item backlog is 4, so at most four decisions re-opened per quarter, about one a fortnight instead of several a week.

What would change this call: if PMs ignore the citations and skim the ranking anyway, then provenance was not the constraint, and the cheaper Buy / API option was right all along. That shows up in the reversal rate staying flat while time drops, and it is the first thing to watch after launch.
