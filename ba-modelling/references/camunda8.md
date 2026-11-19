# Camunda 8: Modeler practice and XML conventions

(Examples use a neutral order-handling process.)

## Camunda Modeler in practice (what we ran into)
- **Lanes**: no lane tool in the palette. Click an existing lane → context pad **"Add lane above / below"**. Lanes cannot be dragged to another position (not even in a pool): add a lane at the right place, drag the elements in, delete the old lane. The first lane of a sub-process page without lanes can only be added in the XML.
- **Lanes in sub-process pages** display and edit fine (Desktop and Web Modeler), but cannot be created from scratch there.
- **Collapsed sub-process**: the parent shape needs `isExpanded="false"`; its content lives on its own `BPMNDiagram` plane. The blue arrow icon (drill down) is UI only and does not appear in exports.
- **Problems panel**: "process is not executable" is expected (models need not be executable). Do not set `isExecutable="true"`: that triggers ~170 implementation errors (job types, message refs, FEEL conditions).
- **Web Modeler artefact**: it can write `bounds="[object Object]"` on `BPMNPlane` elements. Harmless for Camunda, invalid for other tools; remove when cleaning up.
- **No XML editor in the Modeler**: paste XML fragments in a text editor, then re-open/import. A sub-process fragment needs both parts: the semantic `<bpmn:subProcess>` (keep the parent's incoming/outgoing ids) and its `<bpmndi:BPMNDiagram>` plane.
- **Exports**: File › Export As Image per diagram page (PNG/SVG). Decision tables are not exported as images: screenshot them or reproduce them as tables.
- **Team work**: one person edits a file at a time in Web Modeler; keep the repository copy in sync after each session, otherwise versions diverge.


## XML conventions

The models must open in Camunda Modeler as **Camunda 8** diagrams. Write the semantics by hand, then
let `node .claude/skills/bpmn/scripts/bpmn-tool.mjs layout <file>` generate the BPMN DI. The DMN DI
(DRD shapes) you write yourself: there are only a handful of boxes.

## BPMN definitions header

```xml
<?xml version="1.0" encoding="UTF-8"?>
<bpmn:definitions xmlns:bpmn="http://www.omg.org/spec/BPMN/20100524/MODEL"
  xmlns:zeebe="http://camunda.org/schema/zeebe/1.0"
  xmlns:modeler="http://camunda.org/schema/modeler/1.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  id="Definitions_Acme" targetNamespace="http://bpmn.io/schema/bpmn"
  exporter="Camunda Modeler" exporterVersion="5.30.0"
  modeler:executionPlatform="Camunda Cloud" modeler:executionPlatformVersion="8.6.0">
```

## Collaboration, black-box pools, lanes

```xml
<bpmn:collaboration id="Collab_B2C">
  <bpmn:participant id="P_Customer" name="Customer" />              <!-- black box: no processRef -->
  <bpmn:participant id="P_Acme" name="Acme" processRef="Process_Acme" />
  <bpmn:participant id="P_Supplier" name="Supplier" />
  <bpmn:messageFlow id="MF_Request" name="order request" sourceRef="P_Customer" targetRef="Start_Request" />
  <bpmn:messageFlow id="MF_Proposal" name="order confirmation" sourceRef="Throw_Proposal" targetRef="P_Customer" />
</bpmn:collaboration>
<bpmn:process id="Process_Acme" name="Order-to-cash" isExecutable="false">
  <bpmn:laneSet id="LaneSet_1">
    <bpmn:lane id="Lane_System" name="Order system">
      <bpmn:flowNodeRef>Start_Request</bpmn:flowNodeRef>
      <!-- every flow node of the process (top level, not inside sub-processes) in exactly one lane -->
    </bpmn:lane>
    <bpmn:lane id="Lane_CSR" name="Customer service representative" />
  </bpmn:laneSet>
  ...
</bpmn:process>
```
Message flows to a black-box pool use the participant id as `sourceRef`/`targetRef`.
A message flow may also start or end on a **collapsed sub-process** (the sub-process exchanges
messages internally). Do not attach message flows to elements inside a collapsed sub-process from the
top-level diagram: they would be drawn across pages.

## Business rule task linked to DMN

```xml
<bpmn:businessRuleTask id="BRT_Approval" name="Determine approval">
  <bpmn:extensionElements>
    <zeebe:calledDecision decisionId="Approval" resultVariable="approval" />
  </bpmn:extensionElements>
  <bpmn:incoming>...</bpmn:incoming><bpmn:outgoing>...</bpmn:outgoing>
</bpmn:businessRuleTask>
```
The XOR after it routes on the result: gateway name "Approval outcome?", flows named
"approve", "ask for more information", ... Optionally add a FEEL condition:
`<bpmn:conditionExpression xsi:type="bpmn:tFormalExpression">=approval.approvalOutcome = "Decline request"</bpmn:conditionExpression>`.

## Collapsed sub-process (own page in the modeler)

```xml
<bpmn:subProcess id="SP_Prepare" name="Prepare delivery">
  <bpmn:incoming>F_1</bpmn:incoming><bpmn:outgoing>F_2</bpmn:outgoing>
  <bpmn:startEvent id="SP_Prepare_Start" name="Order confirmed" />
  ... its own flow elements and sequence flows ...
  <bpmn:endEvent id="SP_Prepare_End" name="Delivery prepared" />
</bpmn:subProcess>
```
`layout` gives it a separate `BPMNDiagram` page (collapsed on the parent). To show roles inside it,
add a `<bpmn:laneSet>` with `<bpmn:lane>` + `<bpmn:flowNodeRef>` for every flow node of the sub-process
(right after its incoming/outgoing) and a `BPMNShape` with `isHorizontal="true"` per lane on its plane.
Reuse the pool's role names. The lane of the sub-process on the parent page = the role responsible
for that phase; say so in the description.

## Events

```xml
<!-- timer catch: relative duration -->
<bpmn:intermediateCatchEvent id="Timer_3d" name="3 days passed">
  <bpmn:timerEventDefinition id="TD_1"><bpmn:timeDuration xsi:type="bpmn:tFormalExpression">P3D</bpmn:timeDuration></bpmn:timerEventDefinition>
</bpmn:intermediateCatchEvent>
<!-- message catch / throw -->
<bpmn:intermediateCatchEvent id="Catch_Reply" name="Reply received"><bpmn:messageEventDefinition id="MED_1" /></bpmn:intermediateCatchEvent>
<bpmn:intermediateThrowEvent id="Throw_Proposal" name="Order confirmation sent"><bpmn:messageEventDefinition id="MED_2" /></bpmn:intermediateThrowEvent>
<!-- event-based gateway: outgoing flows go straight to catch events -->
<bpmn:eventBasedGateway id="EBG_1" name="" />
<!-- boundary events -->
<bpmn:boundaryEvent id="B_Cancel" name="Cancellation requested" attachedToRef="SP_Prepare"><bpmn:messageEventDefinition id="MED_3" /></bpmn:boundaryEvent>
<bpmn:boundaryEvent id="B_Remind" name="72 hours passed" cancelActivity="false" attachedToRef="..."><bpmn:timerEventDefinition id="TD_2"><bpmn:timeDuration xsi:type="bpmn:tFormalExpression">PT72H</bpmn:timeDuration></bpmn:timerEventDefinition></bpmn:boundaryEvent>
<!-- error: declare at definitions level, throw with end event inside the sub-process, catch on its boundary, same name -->
<bpmn:error id="Error_NotAvailable" name="Item not available" errorCode="NOT_AVAILABLE" />
<bpmn:endEvent id="E_Err" name="Item not available"><bpmn:errorEventDefinition id="EED_1" errorRef="Error_NotAvailable" /></bpmn:endEvent>
<bpmn:boundaryEvent id="B_Err" name="Item not available" attachedToRef="SP_X"><bpmn:errorEventDefinition id="EED_2" errorRef="Error_NotAvailable" /></bpmn:boundaryEvent>
```
Message definitions (`bpmn:message`) are optional for a non-executable model.
Text annotations: `<bpmn:textAnnotation id="TA_1"><bpmn:text>...</bpmn:text></bpmn:textAnnotation>` +
`<bpmn:association id="A_1" sourceRef="TA_1" targetRef="Task_X" />` (inside the process).

## DMN (DMN 1.3 for Camunda 8)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<definitions xmlns="https://www.omg.org/spec/DMN/20191111/MODEL/"
  xmlns:dmndi="https://www.omg.org/spec/DMN/20191111/DMNDI/"
  xmlns:dc="http://www.omg.org/spec/DMN/20180521/DC/"
  xmlns:di="http://www.omg.org/spec/DMN/20180521/DI/"
  xmlns:modeler="http://camunda.org/schema/modeler/1.0"
  id="Definitions_Approval" name="Approval" namespace="http://camunda.org/schema/1.0/dmn"
  exporter="Camunda Modeler" exporterVersion="5.30.0"
  modeler:executionPlatform="Camunda Cloud" modeler:executionPlatformVersion="8.6.0">
  <decision id="Approval" name="Approval">
    <informationRequirement id="IR_SP_Risk"><requiredDecision href="#RiskCategory" /></informationRequirement>
    <informationRequirement id="IR_SP_Avail"><requiredInput href="#Input_Customer" /></informationRequirement>
    <decisionTable id="DT_Approval" hitPolicy="UNIQUE">   <!-- UNIQUE | FIRST | PRIORITY | ANY | COLLECT | RULE ORDER | OUTPUT ORDER -->
      <input id="I_SP_1" label="Risk category">
        <inputExpression id="IE_SP_1" typeRef="string"><text>RiskCategory</text></inputExpression>
      </input>
      <input id="I_SP_2" label="Customer type">
        <inputExpression id="IE_SP_2" typeRef="string"><text>customerType</text></inputExpression>
        <inputValues id="IV_SP_2"><text>"Private","Business"</text></inputValues>
      </input>
      <output id="O_SP_1" label="Risk category" name="riskCategory" typeRef="number" />
      <output id="O_SP_2" label="Approval outcome" name="approvalOutcome" typeRef="string">
        <outputValues id="OV_SP_2"><text>"Approve","Approve with conditions","Ask for more information","Decline request"</text></outputValues>
      </output>
      <rule id="R_SP_1">
        <description>optional annotation shown in the Annotations column</description>
        <inputEntry id="R_SP_1_i1"><text>-</text></inputEntry>
        <inputEntry id="R_SP_1_i2"><text>"Business"</text></inputEntry>
        <outputEntry id="R_SP_1_o1"><text>RiskCategory</text></outputEntry>
        <outputEntry id="R_SP_1_o2"><text>"Approve"</text></outputEntry>
      </rule>
    </decisionTable>
  </decision>
  <inputData id="Input_Customer" name="Customer data" />
  <knowledgeSource id="KS_Policy" name="Credit policy" />   <!-- optional -->
  <dmndi:DMNDI>
    <dmndi:DMNDiagram id="DMNDiagram_Approval">
      <dmndi:DMNShape id="DMNShape_Approval" dmnElementRef="Approval"><dc:Bounds height="80" width="180" x="300" y="80" /></dmndi:DMNShape>
      <dmndi:DMNShape id="DMNShape_Avail" dmnElementRef="Input_Customer"><dc:Bounds height="45" width="125" x="327" y="260" /></dmndi:DMNShape>
      <dmndi:DMNEdge id="DMNEdge_IR_SP_Avail" dmnElementRef="IR_SP_Avail"><di:waypoint x="390" y="260" /><di:waypoint x="390" y="160" /></dmndi:DMNEdge>
    </dmndi:DMNDiagram>
  </dmndi:DMNDI>
</definitions>
```
Rules:
- Every `informationRequirement` needs its own id and a `DMNEdge` (waypoints: from the bottom/top
  centre of the required element to the requiring decision; arrows point upward to the decision).
- Every decision, input data, BKM and knowledge source needs a `DMNShape`. Decisions 180×80, input
  data 125×45. Lay out bottom-up: input data at the bottom, sub-decisions in the middle, top decision on top.
- **One DMN file per DRD.** Camunda 8 deploys each DMN file separately; a business rule task can call
  any decision in any deployed file. Use one file per decision cluster: e.g. `approval.dmn`
  (Approval + Risk category), `discount.dmn` (Discount + Customer segment), and
  optional ones (`deposit.dmn`, ...). A decision required by another decision must be in the same file.
- XML-escape `<` and `>` in FEEL entries: `&lt; 72`, `&gt;= 4`.
- FEEL entries: `"Business"`, `"Gold","Silver"`, `not("Gold")`, `> 120`, `[2..3]`, `[1000..3000)`,
  `true`, `-` (any). Comparisons with another input are allowed: `>= requiredAmount`.
- Add a `<description>` to rules where the reason is not obvious (it shows in the Annotations column).
