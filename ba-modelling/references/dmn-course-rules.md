# DMN rules

## DMN or control flow?
- Do not hard-code decision trees as gateway cascades. A business rule task calls the decision; the gateway after it only routes on the result.
- Criterion we use (write it in the description): **DMN when several inputs together decide the outcome or when it is a tariff/threshold policy; a gateway when one yes/no condition routes the flow.**
- Check which decisions the assignment makes mandatory in DMN; for the others, the choice must be motivated.
- A later gateway may reuse a stored decision result (e.g. a later "Rental needed?" gateway reads the outcome of an earlier planning decision). No second BRT, no second DMN.

## DRD
- Decision (rectangle), input data (rounded), information requirement (solid arrow). The top decision is the goal; the DRD imposes no order.
- Split a sub-decision when a concept is a conclusion in one sentence and a condition in another (e.g. a customer status that feeds a service level).
- Knowledge sources and BKMs: optional. Rare in practice and usually not required for a course assignment. Omit them and say so.
- A decision required by another decision must be in the same DMN file (Camunda).

## Decision tables
- Rows are rules (AND across columns), "-" = irrelevant, a comma list inside a cell = OR.
- Hit policies: **U** unique (default, no overlap) · **A** any (overlap only with equal output) · **F** first (order matters, hard to validate) · **P** priority (highest-priority output wins; order of output values) · **R/O/C** multiple hit (C with +, #, <, >).
- Validation (exam and assignment): **completeness** (every input combination matches), **consistency** (no overlap in U; equal outputs in A; no masked rule in P), **no subsumption** (merge rules that differ in one column with the same output).
- Typical errors: gaps at boundaries ("< 2000" and "> 2000" miss 2000), "from X to Y" read as exclusive, OR conditions written as one row, overriding rules ("regardless of…") that a priority table lets the wrong rule win, a column that is "-" in every rule.
- Declare input/output values for enumerations; add rule annotations for the case sentence behind each rule.

## How we validate a table
1. Turn every sentence of the case into a test case (inputs → expected output), including the boundary values (exactly on every threshold).
2. Run the tests against the table (`check.mjs` evaluates FEEL and reports failures).
3. Let the grid analysis prove completeness, overlap, masked/unreachable rules and merge candidates.
4. Record every interpretation of vague wording as an assumption.
