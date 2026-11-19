# BPMN rules and recurring grading points

## Recurring grading points
- Sub-processes without a start or end event are among the most frequent mistakes. Every sub-process and every pool must be a valid process on its own.
- A collapsed sub-process is a separate diagram/page, not a fragment drawn inside the parent.
- Decompose as soon as a diagram is no longer legible on one A4 page (models are often graded on paper).
- Layout is graded: messy models with arrows in every direction lose points.
- 7PMG compliance and hierarchical, structured models are expected explicitly.
- No arrows from right to left (snake models), and no link events to hide them: decompose instead.
- An inclusive OR added "to be safe" or to patch a deadlock is the worst choice; OR only for a real "one or more" case, as a block.
- Event-based gateways, interrupting and non-interrupting boundary events are expected in a realistic case.
- Prefer black-box pools for customers and suppliers; focus on the company's own process.
- Data modelling happens elsewhere: only add data objects that add information.
- Work order: scope → list of tasks → handovers → gateways. Starting with boxes and arrows ends in XOR chaos.

## Gateways
| Gateway | Behaviour | Use when |
|---|---|---|
| XOR | one branch / continue when one arrives | data known now; **conditions on every outgoing arc**, question label on the gateway |
| AND | all branches / wait for all | independent work that must all happen |
| OR | one or more / wait for active | genuine "one or more" only, as a block |
| Event-based | first event wins | waiting: answer vs deadline; outgoing arcs go **directly** to catching events |
- Draw all gateways explicitly (implicit AND-split/XOR-join allowed by BPMN, but bad practice).
- Matching blocks: an AND split is closed by an AND join of the same split. A loop re-entering one branch of an AND block deadlocks.

## Sub-processes
- Decompose at ~30–50 elements or when not legible on A4. Aim for a value-chain top level.
- Own start and end events; sequence flows never cross the border (message flows may).
- Several end events = several outcomes → XOR in the parent that routes on each outcome.
- Loop marker (with annotated stop condition) or structured backward XOR for repetition; multi-instance for "for each" with known cardinality.

## Events
- Start catches, end throws. One start preferably; several ends for distinct outcomes are fine.
- Type events when the trigger is known. Allowed: none, message, timer, escalation, error, compensation (not needed), signal, terminate, link (avoid). Not used: conditional, cancel, multiple.
- Task vs event: effort and time → task; instantaneous/automated send or receive → event.
- **A timer marks a moment in time ("3 days before the deadline"), never an occurrence ("staff left the office").**
- Interrupting boundary (solid) stops the activity; non-interrupting (dashed) runs in parallel. Event sub-process = the same for a whole process.
- Internal error: error end event in the sub-process + boundary error event with the same name. Terminate: ends that (sub-)process only.

## Pools, lanes, data
- One organisation = one pool; roles/departments = lanes; abstract roles only.
- Sequence flows never cross pools; message flows never between lanes of the same pool.
- Lanes inside sub-process pages are valid BPMN (SubProcess has laneSets); reuse the pool's role names.
- Data objects/stores only when the relation is non-obvious.

## Quality
- Syntax: structural rules + soundness (option to complete, proper completion, no dead transitions; no deadlock, livelock, lack of synchronisation).
- Semantics: correct (only allowed behaviour) and complete (all scenarios of the case).
- Pragmatics: size, structure, layout, labels: activities verb-object, events object + past participle, gateways as questions, conditions as answers.
- 7PMG: G1 few elements · G2 few routing paths per element · G3 one start/one end · G4 structured · G5 no OR · G6 verb-object labels · G7 decompose > 50 elements.
