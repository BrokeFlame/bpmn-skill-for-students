# BA modelling skill for Claude

A [Claude skill](https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview) that turns Claude into a disciplined BPMN + DMN modelling partner for university business-process-modelling assignments in **Camunda 8**.

It packs the process a student team used to build, review and deliver a graded booking-to-cash model: what to do in which order, which rules graders check, how to decide between DMN and gateways, how to validate decision tables, and the mistakes worth avoiding.

## What is inside
| File | Content |
|---|---|
| `ba-modelling/SKILL.md` | 8-step process: scope → activities → handovers → control flow → decisions → additional elements → layout and print → review loop |
| `references/bpmn-course-rules.md` | BPMN rules, recurring grading points, 7PMG |
| `references/dmn-course-rules.md` | DRDs, hit policies, completeness/overlap/subsumption, validating tables with test cases |
| `references/patterns-and-pitfalls.md` | proven patterns, 18 common mistakes with fixes, recurring modelling dilemmas |
| `references/camunda8.md` | Camunda Modeler in practice (lanes, sub-processes, exports, XML pasting) and Camunda 8 XML conventions |
| `references/review-checklist.md` | review loop, rubric, checklists per page and per decision, what a structured description needs |

## Install
- **Claude.ai / Claude app:** zip the `ba-modelling` folder and upload it under Settings › Capabilities › Skills.
- **Claude Code:** copy `ba-modelling/` into `.claude/skills/` of your project (or `~/.claude/skills/`).

## Use it responsibly
This skill describes a working method and general BPMN/DMN rules; it contains no solution to any assignment. Follow your course's rules on the use of generative AI and disclose its use where required. You remain responsible for your models.

## Credits
Written by a student team (KU Leuven, Business Analysis 2026-2027) with Claude. Optional validation tooling: the [`bpmn` skill](https://github.com/architawr/claude-bpmn-skill) by architawr (MIT).

## License
MIT, see `LICENSE`.
