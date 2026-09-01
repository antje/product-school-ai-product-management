# AI PRD · Juno

> The requirements for Juno's evidence engine: what it retrieves, how, at what cost, and what it does when the evidence is not there.

## Problem & user

RocketShip's roadmap gets ranked by what is loudest and most recent, not by evidence. In the weekly review a product manager orders around 40 open items from memory and from whatever escalated in Slack that week. When someone senior challenges a rank, the PM cannot produce the reason in the meeting. So the argument goes to whoever pushed hardest, and the decision quietly reverses the week after.

The user is that product manager. Juno's job is to turn the noise across Slack, Jira and Notion into a ranked backlog where every position cites the clause that justifies it. The point is not a better ranking. It is a ranking that survives the room.

## Solution overview

Juno drafts, the human decides. It produces a ranked backlog with the evidence attached, and the PM approves before anything is published. We split it that way because a wrong draft costs five minutes to correct, while a wrong entry on the live roadmap moves real work. The risk sits in publishing, so that is where the person stays.

**Retrieval strategy: hybrid.** The knowledge splits into two kinds and each wants opposite handling.

The strategy document is short, changes about once a quarter, and only makes sense read whole. Its exclusion list is what lets Juno say no to something, and that list only works if the whole document is present. Chunk it, and a question about a crashing export would pull the reliability section and never see the list of things the company has decided not to do. Juno would then rank a cosmetic request as reasonable and look like it was working.

Everything else, the Slack threads, the tickets, the Notion pages, changes constantly and runs to thousands of items. That is far too much to hold in a model's context at once, and the task only ever needs a few relevant pieces. So we index it and retrieve.

We are not adding autonomous tool use. Ranking needs Juno to find and order things, not to act on them, and every tool it could call is a tool that can write. Reading one short document whole is a choice about chunking, not a step up in capability.

## Retrieval requirements (RAG)

- **Sources:** RocketShip Q3 2026 Strategy One-Pager, the ranking authority, read whole and never chunked. Slack `#escalations` and `#support`, rolling 90 days. Jira ROCKET project, rolling 90 days. Notion Product workspace, current page versions only.
- **Chunking / indexing:** By natural unit, not fixed windows. One Slack thread is one chunk, one Jira ticket is one chunk, with an 800 token cap and 100 token overlap for longer threads. The strategy document is exempt and passes whole. Reranking is on, ordering strategy document first, then most recent, then most distinct accounts.
- **Grounding rule:** Every ranked item shows the priority tag, the source it came from, and the clause it relied on, quoted word for word. An item missing any of the three is a bug, not a low-confidence answer. Where the strategy and a ticket disagree, the strategy wins and Juno names the clause it followed.
- **Freshness:** Event-driven, not on a schedule. The strategy document re-indexes when it is published, Slack when a thread opens or closes, Jira when a ticket is created or changes status, Notion when a page is published. Worst case, the index is one hour behind.

**Why these sources and this window.** Retrieval is limited to the requesting team's own data and to threads and tickets linked to the backlog under review, so one team's escalations cannot inflate another team's ranking. Direct messages, private channels, contracts and revenue figures are left out on purpose. Juno already refuses to answer revenue questions, so keeping that data out of the index enforces a rule it already follows. The 90 day window is deliberate too: a priority argued on evidence older than a quarter is arguing about a product that has already moved on.

**Why chunk by natural unit.** Splitting on a fixed word count would cut an escalation in half, leaving the complaint in one piece and its consequence in another. A rank that cites only half an argument is worse than no rank at all, because it looks sourced. Reranking is on so that the conflict rule has somewhere to run. Without it, an old Notion page can outrank the current strategy simply by using similar words.

**Why hybrid search.** The PM asks two different kinds of question. One is conceptual: what evidence says this is a reliability problem? That needs meaning-based search, because nobody writes the phrase "reliability problem" in a support ticket. The other is exact: show me everything on ROCKET-4421, or everything Acme raised this month. That needs keyword search, because meaning-based search returns things that look like a ticket number rather than that ticket. Every rank needs both a theme and a receipt, so both run together.

**Why one hour.** The output is read in a weekly meeting. The oldest thing that could still change a rank is whatever escalated in the hour before it starts. Syncing faster costs more and changes nothing a weekly rhythm can use.

## Requirements

### Functional requirements

| # | Requirement | Priority | Acceptance criteria |
|---|---|---|---|
| 1 | Retrieval budget | Must | Top 8 segments retrieved per query. 95% of queries return in under 4 seconds, measured over 100 consecutive runs. Hard ceiling of 6,000 tokens per query; the build fails above it. |
| 2 | Every rank is traceable | Must | 100% of ranked items show a priority tag, a source, and a quoted clause. An item missing any of the three fails the build. |
| 3 | Evidence breadth is visible | Must | Every item shows how many separate customers the evidence covers, without the reader opening anything. |
| 4 | Refuses rather than guesses | Must | When evidence is thin, Juno returns the refusal wording below and sends the item to the review queue marked unranked. Nothing is dropped silently or given a default priority. |
| 5 | The tag is the claim, not the score | Must | No test asserts an exact alignment score. Tests assert the priority tag and the presence of a quoted clause. |
| 6 | Exclusions quoted, not summarized | Should | Where an item is not recommended, the interface shows the strategy's own wording rather than a paraphrase. |

**Why eight segments.** Juno ranks by how many separate customers are affected, not by how many messages were sent, and it shows that count. Establishing that count takes two or three pieces of evidence per item, so covering the top three items needs about eight. Below six, a case resting on one customer stops looking thin. Above ten, the cost per query climbs without surfacing anyone new.

**Why four seconds.** The output is reviewed in a 30 minute meeting covering about 40 items, so roughly 30 seconds of attention each. A four second wait is a small fraction of that and disappears into the reading. This is a batch review, not something someone types into and waits on, so a tighter target would cost money for a delay nobody feels.

**Why a token ceiling.** Without one, adding a source raises the cost of every query and nobody notices until the bill arrives. The ceiling forces that to be a decision.

**Why the tag and not the score.** In testing, the same transcript produced different alignment scores from one run to the next, 63 then 60 on one item and 10 then 0 on another, while the priority tag and the quoted clause stayed the same. A test written against an exact score would fail on a system that is working correctly.

### Failure modes and guardrails

| Failure mode | What it looks like | Guardrail |
|---|---|---|
| Opinion dressed as evidence | A rank cites a real thread, but the record over-represents whoever posts most. One customer raising the same issue twelve times reads as twelve signals. | Rank by separate customers affected, never message count. Show that number. Nothing reaches top priority on evidence from a single customer. |
| Quiet staleness | The strategy changes, the index does not, and Juno keeps refusing things the company has started doing again. | Re-index on publish. Show when the strategy was last indexed next to every ranking. |
| Filling a gap with a guess | A confident-looking rank built on nothing retrieved, indistinguishable from a sourced one once it sits in the list. | The refusal wording below, plus routing to the review queue. Never a default priority. |
| Sources that disagree | A ticket and the strategy imply different priorities and Juno silently picks one. | Reranking puts the strategy first. Where a real contradiction remains, Juno refuses and routes rather than choosing. |
| Evidence from the wrong team | Another team's escalations lift an item in this team's ranking. | Retrieval is limited to the requesting team's data, enforced when the index is built rather than filtered afterwards. |

**The refusal wording, exactly:**

> "Insufficient evidence to rank this item. I retrieved [n] sources and none of them are from the strategy document. To rank it I need [the specific missing thing]. Sending this to you for a manual call."

**Why refuse rather than guess.** This protects the user, not the team that built it. A PM who is told what is missing can go and find it in a minute. A PM handed a confident rank built on nothing loses an argument in front of leadership and never trusts the tool again. Saying "I do not know" costs a minute. Guessing costs the product.

## Out of scope

1. Writing to the live roadmap. No dates, no sprint assignment, no status changes. Juno drafts, the PM publishes.
2. Anything involving headcount or hiring.
3. Telling customers why something was deprioritized.
4. Ranking against data Juno cannot see: contracts, revenue figures, private channels and direct messages. It refuses rather than estimating.
