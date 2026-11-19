# Patterns, pitfalls and recurring dilemmas

## Proven patterns
| Situation | Pattern |
|---|---|
| Wait for a reply with a deadline | event-based gateway → message catch(es) + timer catch |
| Reminder, then a final deadline | EBG → [reply \| timer → reminder sent → EBG → [reply \| timer]] |
| Customer may change or cancel "any time until X" | interrupting message boundary event on the sub-process spanning that period |
| Something happens during a phase but the phase continues | non-interrupting boundary event |
| Payment can arrive before or during the dunning procedure | interrupting "Payment received" boundary on the dunning sub-process, then an XOR merge |
| A phase can end in different ways | several end events in the sub-process + an XOR in the parent that routes on the outcome |
| Two things "at the same time" | AND split + matching AND join |
| Handover between roles of the same organisation | sequence flow to a task in the other lane (no message, no link events); keep those lanes adjacent |
| A decision result is needed again later | the later gateway reads the stored result of the earlier business rule task |
| A loop back after a change (re-plan, re-check) | re-enter just before the decision, behind any one-time intake steps |
| A benefit earned now and used next time (voucher, credit) | at the next relevant step: valid? → customer uses it? → apply; then determine the new benefit |
| Automated notifications | throwing message events (the system sends, no human effort) |

## Common mistakes and their fix
1. **Timer for an occurrence** ("staff departed") → a task or a catching message event; a timer only marks a moment in time.
2. **Timer for a deadline** ("within 24 hours" before the task) → that means *wait 24 h*. Put the deadline in the task name or annotation, or use a timer boundary only if the case says what happens when it is missed.
3. **Chained timers** that count from the wrong moment ("next working day" after another wait) → start each timer from the event it refers to.
4. **Link events to connect lanes or fragments** → sequence flows; decompose instead of snaking.
5. **A loop that re-enters before one-time steps** (registration repeated on every re-plan) → re-enter right before the step that must be repeated.
6. **One join closing two different splits**, or two flows entering the same side of a gateway → every split gets its own join; at most one flow per gateway side on paper.
7. **Sequence where the case says "at the same time"** → AND block.
8. **Gateway question broader than the case condition** (asking about the customer type when the condition is about the invoice type) → copy the exact condition.
9. **Checks in an illogical order** (does the customer want to use it? before is there one?) → existence/validity first.
10. **"From X to Y" written as strict inequalities** → inclusive ranges; test the exact boundary values.
11. **Overlapping rules** (a general rule and an exception both match) → make the exception win explicitly and the rules disjoint.
12. **Priority table where an override ("regardless of…") loses to a higher-priority output** → add the opposite condition to the competing rules.
13. **A column that is "-" in every rule** → turn it into a computed input (e.g. `available >= required`) or drop it.
14. **Every decision in DMN without a stated criterion** → state when you use DMN and when a gateway.
15. **Inconsistent task types** → one convention for the whole model, stated in the description.
16. **An interpretation that needs data the case never collects** → prefer the reading that works with the defined inputs; keep model, tables, tests and assumptions aligned.
17. **Pages below the minimum font size after a small change** → re-measure after every layout change.
18. **Labels on lines or borders, icons over three-line labels** → wider tasks, ≤ 2-line labels, look at every rendered page.

## Recurring dilemmas (decide once, write it in the description)
| Dilemma | Option A | Option B | Guidance |
|---|---|---|---|
| Closing an outer AND split that contains an inner AND block | one join for all branches (canonical block, G4) | intermediate join, max 3 inputs per gateway (G2) | both sound; choose A only if no two flows share a gateway side on paper |
| Activities that may overlap but the case gives no order | AND block | sequence | sequence is fine for readability (state it); AND when the case says "at the same time" |
| Several roles within one department | one lane per role | one lane for the group + role in brackets in the task name | both valid (lane = role or group); B saves space |
| External workers acting under the company's direction (freelancers) | lane in the company pool | separate black-box pool | lane keeps handovers as sequence flows; justify it |
| Two customer actions with the same business meaning | one catching event naming both triggers | two events | one event is fine if the label is explicit |
| System sends vs person sends | throwing message event | send task | system = event, person = task |
| No-show / arrival | EBG: "arrival reported" vs "30 min after start" timer | conditional event | conditional events are often excluded in courses → EBG |
| Important assumption | text annotation on the element | only in the description | annotate the few a reader needs to understand the page |

## Questions for the lecturer
Keep a list of open questions with the assumption chosen for each, and model with that assumption until it is answered. Ask concrete questions only.
