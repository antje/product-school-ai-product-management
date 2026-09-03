# Trust-Gap Mitigations · Juno

> Three gaps, scored, with what closes each one and what is still open. Plus the intelligence tax, which is a product surface rather than an implementation detail.

## Value to UX treatment

UX patterns follow the value frame, not taste. Juno's frame is risk mitigation first, cost reduction second.

| Value frame | UX treatment | How Juno does it |
|---|---|---|
| Risk mitigation, primary | Proactive flag + reason + one-click action | A thread crossing the trigger gets a threaded reply carrying the ranked shortlist, each item with its quoted clause, and an approve or edit action on the staged writes |
| Cost reduction, secondary | Background automation with audit trail | The ranking is built before Friday's review rather than during it. Every item carries its source ids, so the audit trail is the output |

The other two frames are deliberately unused. **Revenue generation** would put an inline assist at the moment of decision, but Juno's decision moment belongs to the PM and inserting a button into it adds a step. **User delight** would mean anticipatory dismissible suggestions, which is the pattern most likely to train someone to ignore the product. A P0 triage that is easy to dismiss is a P0 triage nobody reads.

## Trust gaps

Scored on how far each gap is closed, not on how serious it would be if open.

**5** closed, no residual anyone has named. **4** closed on the common path, with a named residual that is accepted. **3** partly closed: the user can act, but cannot fully steer. **2** mitigated by convention only, nothing enforces it. **1** open.


| Gap | Score | Where it shows up | User cost | Mitigation |
|---|---|---|---|---|
| Black-box, no visible "why" | 4/5 | The shortlist card, where an item carries a priority badge and a rank | The PM cannot defend the order in the thread, which is the whole job | Every item quotes the strategy clause behind its priority and shows the evidence line, "4 accounts, 2 enterprise". No item is drafted without a clause to quote |
| Hallucination, confidently wrong | 4/5 | A quoted clause, a customer name, or an evidence count that is not in the sources | A fabricated citation is indistinguishable from a real one, and it is quoted onward into a decision | Clause fidelity is a hard release gate at 100%: every quoted clause must appear verbatim in the strategy document. Thin or contradictory evidence routes to a refusal that names the missing evidence rather than ranking anyway |
| Control, cannot steer or stop | 3/5 | The staged writes, and the next run after a rejection | The PM can stop a bad item but cannot stop it coming back | Every write is staged and the PM approves, edits or rejects each item. A run can be cancelled in flight, and Juno can be disabled per channel by the PM who owns it. Staged batches expire after four hours |
| Intelligence tax | 5/5 | The 45 seconds between the trigger firing and the shortlist arriving. Retrieval is budgeted at 4s p95 per call, the whole run at 45s wall clock | Normally, waiting. Here, close to nothing | The wait is asynchronous: the PM is not watching a screen, the shortlist arrives in a thread they return to. No streaming and no "Juno is reasoning" indicator, because a placeholder posted into an argument adds noise to the thing it is trying to settle |

### What is still open on each

**Black-box, 4 not 5.** The PM sees the clause that justified an item, but not what Juno retrieved and discarded. An item ranked third because a competing item had more affected accounts shows its own count, not the comparison. Closing this means surfacing the runners-up, which costs card space, so it stays open on purpose for now.

**Hallucination, 4 not 5.** A verbatim clause match proves the clause exists and is quoted accurately. It does not prove the clause supports the rank it carries. That check is the LLM-judge layer, which samples 20 items nightly rather than gating every run, so a clause that is real but irrelevant can reach the PM between grading rounds.

**Control, 3 and the lowest score here.** The PM can stop any single item and any single run. What they cannot do is tell Juno not to raise something again. Semantic memory deliberately holds no learned view of which accounts or items matter, because that is exactly the memory that turns into bias over a few weeks. The cost of that decision lands here: a rejected item can return next week at the same rank, with the same evidence, because the evidence has not changed.

**When the tax would exceed the value.** The kill signal is already instrumented: a run is discarded if the thread gains more than ten new messages while it is in flight. If more than 10% of runs are discarded that way, the shortlist is routinely landing after the argument has moved on, and the trigger or the budget is wrong rather than the feature being slow. Below that, the latency is buying accuracy and is worth paying.

**Intelligence tax, 5/5 and worth stating plainly.** The tax is low because of where the product sits, not because the model is fast. A 45 second wait in a chat window is a bad experience. The same 45 seconds in a Slack thread the PM comes back to is invisible. If Juno had shipped as the dashboard the prototype used, this row would score 2 and the latency budget would have to be defended rather than spent.

## Highest-priority fix

**The control gap, specifically "don't raise this again."**

It is the lowest-scoring gap, but that is not the reason. It is the one that compounds. A PM who rejects the same item three weeks running concludes the tool is not listening, and stops reading carefully rather than stops using it. That failure shows up as the override rate falling toward zero, which is the exact signal the eval stack watches for and the exact reason the approval checkpoint stops being real.

The fix has to respect the memory boundary rather than break it. Storing "this account's issues are low priority" is the bias the semantic memory rule exists to prevent. So instead: record each rejection against the item's root cause in the run log, which already captures it. On a later run, if a matching root cause reappears within 30 days, Juno still ranks it on the evidence and annotates it, "rejected 12 days ago, reason: known and accepted".

30 days, because the strategy document is revised quarterly. A rejection older than half a cycle may rest on an exclusion that has since changed, and re-raising the item then is legitimate rather than repetitive.

That closes the gap the PM actually feels, which is not being ignored, without teaching Juno an opinion about who complains loudest. The ranking stays evidence-based. The PM stops repeating themselves.

## Self-review

- [x] All three gaps scored, with the mitigation named for each.
- [x] The intelligence tax has its own row, with a latency figure and a streaming decision.
- [x] Value frames are mapped to UX treatments, and the unused frames are named with reasons.
- [x] Each gap says what is still open, not only what is closed.
- [x] The highest-priority fix names one gap and gives the reason it ranks first.
- [x] The proposed fix is consistent with the memory boundaries already committed.
