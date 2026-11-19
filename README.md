# BA modelling skill for Claude

A [Claude skill](https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview) that turns Claude into a disciplined BPMN + DMN modelling partner for university business-process-modelling assignments in **Camunda 8**.

It packs the process a student team used to build, review and deliver a graded booking-to-cash model: what to do in which order, which rules graders check, how to decide between DMN and gateways, how to validate decision tables, and the mistakes worth avoiding.

## Background
This skill was created in the context of a group assignment for the course *Business Analysis* (KU Leuven, academic year 2026-2027). As a team of five students, we had to model a company's booking-to-cash process in BPMN and its business decisions in DMN, using Camunda 8. While working on that task, we collected our working method, the course's quality rules and the lessons from our review rounds in this skill, so that it can help other students with similar modelling assignments. It describes how to model, not what to model: it contains no part of our submitted solution.

## Sources and references
The rules and methods in this skill are summarised in our own words from the sources below; no slides, recordings or texts are copied. Cite these sources, not this skill, when you use the underlying concepts.

- De Weerdt, J. (2026). *Business Analysis* [course lectures BPM2, BPM3, BPM5, BPM6]. KU Leuven. Source of the modelling method and the recurring grading points.
- Dumas, M., La Rosa, M., Mendling, J., & Reijers, H. A. (2018). *Fundamentals of Business Process Management* (2nd ed.). Springer.
- Mendling, J., Reijers, H. A., & van der Aalst, W. M. P. (2010). Seven process modeling guidelines (7PMG). *Information and Software Technology, 52*(2), 127–136.
- Object Management Group. *Business Process Model and Notation (BPMN) 2.0.2* and *Decision Model and Notation (DMN)*. https://www.omg.org/spec/
- architawr. *claude-bpmn-skill* (MIT licence). https://github.com/architawr/claude-bpmn-skill. Mentioned as optional validation tooling; no code included.

Written with the help of Claude (Anthropic); we reviewed, corrected and restructured all content ourselves.

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
Written by a student team (KU Leuven, Business Analysis 2026-2027) with Claude. See *Sources and references* above for all material we built on.

## License
MIT, see `LICENSE`.
