# Review checklist and loop

## Loop
1. Build or change the model (semantics first, then layout).
2. Run the automated checks you have (validation, lint, decision-table tests, A4 measurement) and render every page.
3. Look at **every** rendered page and the print PDF: automated checks do not see layout.
4. Walk through the case sentence by sentence and every exception with a token: where does it go, does it end?
5. Write findings as numbered issues: **blocker** (not graded / wrong semantics), **major** (grader will deduct), **minor** (polish). Fix blockers and majors first.
6. Stop when: 0 check errors, all DMN tests pass, every page ≥ 6 pt (aim ≥ 6.5), ≤ 12 pages, no open blocker/major, every warning fixed or justified in `design.md`.
When reviewing a teammate's or AI's work: report, do not silently change their model.

## Rubric (1–5 each)
| Criterion | 5 means |
|---|---|
| Readability and layout | left-to-right, no crossings, no labels on lines/borders, consistent sizes, value-chain top level, legible on A4 |
| Completeness | every scenario and exception of the case is present |
| Correctness | only allowed behaviour; right gateway/event types; correct roles; deadlines and timers right |
| Syntax and soundness | course rules + no deadlock, lack of sync, dead ends; every block matched |
| Decision modelling | meaningful DRDs, deliberate hit policies, complete/consistent tables, BRT links, DMN-vs-gateway criterion stated |
| 7PMG and labels | G1–G7; verb-object tasks, past-participle events, question gateways, answer conditions |
| Documentation | every assumption numbered, every choice motivated, no case text copied |

## Checklist per page
- [ ] One start event; end event(s) named per outcome; sub-process outcomes routed in the parent.
- [ ] Every XOR split has a question label and a condition on each outgoing flow.
- [ ] Every split has its own matching join; ≤ 3 incoming flows per gateway, one per side.
- [ ] Event-based gateways lead directly to catching events; every wait has its deadline timer.
- [ ] Timers mark moments, not occurrences; automated sends are throwing message events.
- [ ] No link events, no OR gateways (unless a genuine "one or more" block), no right-to-left snake.
- [ ] No message flows inside a pool; no sequence flows across pool/sub-process borders.
- [ ] Every task is in the lane of the role that performs it; lane names match the pool's roles.
- [ ] Every business rule task names its decision and has the right decision ID.
- [ ] Labels: tasks ≤ 2 lines inside the box, nothing touches an icon, a line or a border.
- [ ] Page legible on A4 (≥ 6 pt) in its print grouping.

## Checklist per decision
- [ ] In DMN for a reason (several inputs or a policy), otherwise a gateway.
- [ ] Hit policy U, or another policy with a written reason.
- [ ] Complete, no unintended overlap, no masked rule, no mergeable rules.
- [ ] Boundaries as in the case ("from…to" inclusive, "more than" exclusive).
- [ ] Every case sentence covered by a test case; all tests pass.
- [ ] DRD shows every input and sub-decision; required decisions in the same file.

## Description (2 pages) should contain
Scope and perspective · pools/lanes choice · hierarchy (page list) · how each exception is modelled · DMN-vs-gateway criterion and list · hit policies with reasons · numbered assumptions · deliberate deviations from 7PMG with reason. No retelling of the case.
