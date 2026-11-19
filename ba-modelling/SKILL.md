---
name: ba-modelling
description: >-
  Structured process for building, reviewing and delivering BPMN + DMN models for a
  university business-process-modelling assignment (graded on readability, semantic fit, syntax,
  documentation and the seven process modelling guidelines), in Camunda 8. Use whenever you create,
  edit, review, lay out, print or document a .bpmn or .dmn model, check a model against BPMN/DMN
  quality rules or 7PMG, decide what goes in DMN versus the control flow, or prepare model pages and
  a structured description for submission.
---

# BA modelling process (BPMN + DMN, Camunda 8)

The grader is the lecturer who taught the course. A model that is valid BPMN but ignores the
course's rules loses points: **course rules beat generic BPMN habits.** Typical criteria:
1. readability and layout (printed on A4; text < 6 pt or pixelated = **0** for that part),
2. semantic fit (completeness + correctness against the case),
3. syntactic correctness (language rules + soundness),
4. documentation (2-page structured description),
plus explicit 7PMG compliance. Respect the page limits of the assignment (e.g. 12 model pages,
2 pages description, 1 page AI disclosure).

References (read when the step needs them):
- `references/bpmn-course-rules.md`: BPMN rules and recurring grading points
- `references/dmn-course-rules.md`: DRD, decision tables, hit policies, validation
- `references/patterns-and-pitfalls.md`: proven patterns, common mistakes and recurring dilemmas
- `references/camunda8.md`: Camunda 8 XML, Modeler limitations, exports
- `references/review-checklist.md`: review rubric, checklist and loop

## The process

### 1. Scope (before drawing anything)
Write down: start trigger, end states (one per real outcome: declined, closed, withdrawn,
expired, cancelled, closed without invoice, completed, forwarded to collection), the modelling
perspective (whose process: one company pool, everyone else black-box), and the outcomes.

### 2. List activities and events
Go through the case sentence by sentence and list every activity with its role. Mark per
item: task (takes effort/time), event (instantaneous, system send/receive), decision (→ DMN or
gateway), wait (→ event-based gateway + timer), exception (→ boundary event).
Customer/partner actions are **not** tasks: with black-box pools they become message flows and
catching events.

### 3. Resources and handovers
One pool for the company, lanes for roles/departments (abstract roles, never names).
Black-box pools for the customer and external parties (suppliers, partners, agencies). Handovers between lanes = sequence flows; between pools = message flows only.

### 4. Control flow
First handovers, then gateways. Draw every gateway explicitly, in matching split/join blocks.
Decide the hierarchy: value-chain top level of collapsed sub-processes (one per phase),
≤ ~30–35 elements per page. Each sub-process: own start + end event(s); several outcomes →
several end events + an XOR in the parent that routes on them.

### 5. Decisions
Put a decision in DMN when several inputs together decide the outcome, or when it is a
tariff/threshold policy; keep it as a gateway when one yes/no condition routes the flow. Every
DMN decision is called by a **business rule task** (same name, `zeebe:calledDecision`); gateways
after it only route on the stored result. Split sub-decisions when a concept is a conclusion
in one sentence and a condition in another. Hit policy U by default; any other policy needs a
written reason. Make every case rule a test case and prove tables complete and overlap-free.

### 6. Additional elements
Data objects only when the relation is not obvious from the task name. Text annotations for
assumptions a reader needs on the page. No knowledge
sources/BKMs unless they add something (not required for the assignment).

### 7. Layout and print
Left-to-right, no right-to-left flows except short structured loops, no crossing flows, no
label on a line or a border, max 3 flows into one side of a gateway. Measure every printed
page (≥ 6 pt mandatory, aim ≥ 6.5–7 pt); group small diagrams two or three per A4 page;
decision tables may be reproduced compactly. Total ≤ 12 pages.

### 8. Review loop and documentation
Run the checks, render every page, review against `references/review-checklist.md`, fix,
repeat until no blocker/major issue remains. Keep `assumptions.md` (numbered A1…) and
`design.md` (choices + motivations) in sync with the model: they become the 2-page description.

## Optional tooling
Automated checks save a lot of review time. Useful building blocks:
- the `bpmn` skill (https://github.com/architawr/claude-bpmn-skill): validate, lint, summarize and diff `.bpmn` files;
- `bpmn-js` / `dmn-js` in a headless browser to render every page, DRD and decision table to PNG;
- a FEEL evaluator (e.g. `feelin`) to run test cases against decision tables and enumerate input combinations for completeness and overlap;
- a script that measures the label size of each diagram when printed on A4.
Without tooling, do the same checks by hand with `references/review-checklist.md`.

Done = no syntax or soundness errors, every decision-table test passes, every printed page legible,
every page looked at, every remaining warning justified, model opens in Camunda Modeler.

## Credits
Written by a student team while working on a BPMN/DMN modelling assignment, with Claude. The optional
validation tooling mentioned above is the `bpmn` skill by architawr (MIT licence).
