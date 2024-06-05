<template>
    <div ref="modelerContainer" class="dmn-container"></div>
</template>
<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue';
import DmnModeler from 'dmn-js/lib/Modeler';

const modelerContainer = ref(null);
let dmnModeler;

const diagram = `
    <?xml version="1.0" encoding="UTF-8"?>
<definitions xmlns="https://www.omg.org/spec/DMN/20191111/MODEL/" xmlns:dmndi="https://www.omg.org/spec/DMN/20191111/DMNDI/" xmlns:dc="http://www.omg.org/spec/DMN/20180521/DC/" xmlns:modeler="http://camunda.org/schema/modeler/1.0" xmlns:di="http://www.omg.org/spec/DMN/20180521/DI/" xmlns:magic="http://magic" id="ltv_v1" name="LTV" namespace="http://camunda.org/schema/1.0/dmn" exporter="Camunda Modeler" exporterVersion="5.21.0-dev" modeler:executionPlatform="Camunda Cloud" modeler:executionPlatformVersion="8.4.0">
  <inputData id="residualDuration" name="PRC" magic:spell="Market data (cerberus)" magic:spell2="data.residualDuration" />
  <inputData id="rating" name="Bail-in" magic:spell="Asset taxonomies (copernic)" magic:spell2="rating" />
  <decision id="Decision_0d56nu3" name="Compute PRC">
    <variable id="InformationItem_1sht07o" name="prc2" typeRef="number" />
    <informationRequirement id="InformationRequirement_0unvpqw">
      <requiredInput href="#residualDuration" />
    </informationRequirement>
    <informationRequirement id="InformationRequirement_1t4wsgj">
      <requiredDecision href="#Decision_160rtr2" />
    </informationRequirement>
    <informationRequirement id="InformationRequirement_1ayu0fc">
      <requiredDecision href="#Decision_1x3ug27" />
    </informationRequirement>
    <informationRequirement id="InformationRequirement_0p29tjh">
      <requiredDecision href="#Decision_16uyacj" />
    </informationRequirement>
    <literalExpression id="LiteralExpression_0g87080">
      <text>min(prc+prcMod,prcMin)</text>
    </literalExpression>
  </decision>
  <decision id="Decision_160rtr2" name="Bond">
    <informationRequirement id="InformationRequirement_15lafyx">
      <requiredInput href="#rating" />
    </informationRequirement>
    <informationRequirement id="InformationRequirement_04wqpii">
      <requiredDecision href="#Decision_0ejdbiu" />
    </informationRequirement>
    <decisionTable id="DecisionTable_0t9otw2">
      <input id="InputClause_05v5ex3" label="Bail-in">
        <inputExpression id="LiteralExpression_0nuj5ne" typeRef="boolean">
          <text>bailIn</text>
        </inputExpression>
      </input>
      <output id="OutputClause_1nnaot4" label="Minimal PRC" name="prcMin" typeRef="number" />
      <rule id="DecisionRule_1cfi626">
        <inputEntry id="UnaryTests_0zafggm">
          <text>true</text>
        </inputEntry>
        <outputEntry id="LiteralExpression_0wbljav">
          <text>2</text>
        </outputEntry>
      </rule>
      <rule id="DecisionRule_0oz0qpy">
        <inputEntry id="UnaryTests_10utyn2">
          <text>false</text>
        </inputEntry>
        <outputEntry id="LiteralExpression_17qgfa9">
          <text>1</text>
        </outputEntry>
      </rule>
    </decisionTable>
  </decision>
  <inputData id="InputData_0imjga5" name="Asset&#39;s data" />
  <decision id="Decision_0ejdbiu" name="Check asset&#39;s type">
    <informationRequirement id="InformationRequirement_0j6eu27">
      <requiredInput href="#InputData_0imjga5" />
    </informationRequirement>
  </decision>
  <decision id="Decision_1x3ug27" name="Equity">
    <informationRequirement id="InformationRequirement_0fyesmb">
      <requiredDecision href="#Decision_0ejdbiu" />
    </informationRequirement>
    <informationRequirement id="InformationRequirement_1q4pxoy">
      <requiredInput href="#InputData_0jol6ae" />
    </informationRequirement>
    <informationRequirement id="InformationRequirement_1fv5tii">
      <requiredInput href="#InputData_0m7cyzn" />
    </informationRequirement>
    <decisionTable id="DecisionTable_0zk747u" hitPolicy="COLLECT">
      <input id="InputClause_10ddvr3" label="Market Capitalization">
        <inputExpression id="LiteralExpression_0ofm1c6" typeRef="number">
          <text>marketCap</text>
        </inputExpression>
      </input>
      <input id="InputClause_17udgy5" label="Average Daily Liquidity">
        <inputExpression id="LiteralExpression_0oo4bi7" typeRef="number">
          <text>avgDailyLiquidity</text>
        </inputExpression>
      </input>
      <output id="OutputClause_04rwyce" label="PRC modification" name="prcMod" typeRef="number" />
      <output id="OutputClause_1gp3u3x" label="Minimal PRC" name="prcMin" typeRef="number" />
      <rule id="DecisionRule_1af4zho">
        <inputEntry id="UnaryTests_0rhi0rb">
          <text>&lt; 250000000</text>
        </inputEntry>
        <inputEntry id="UnaryTests_05sthns">
          <text>&lt; 60000</text>
        </inputEntry>
        <outputEntry id="LiteralExpression_0h5glfr">
          <text>1</text>
        </outputEntry>
        <outputEntry id="LiteralExpression_060apv0">
          <text></text>
        </outputEntry>
      </rule>
      <rule id="DecisionRule_0e932rt">
        <inputEntry id="UnaryTests_1kvpi8n">
          <text>&lt; 250000000</text>
        </inputEntry>
        <inputEntry id="UnaryTests_0j6vtjj">
          <text></text>
        </inputEntry>
        <outputEntry id="LiteralExpression_1ouer85">
          <text></text>
        </outputEntry>
        <outputEntry id="LiteralExpression_0eb60au">
          <text>4</text>
        </outputEntry>
      </rule>
    </decisionTable>
  </decision>
  <inputData id="InputData_0jol6ae" name="Market Cap in USD" />
  <inputData id="InputData_0m7cyzn" name="Average Daily Liquidity in USD" />
  <decision id="Decision_16uyacj" name="Fund">
    <informationRequirement id="InformationRequirement_0oh3yhy">
      <requiredInput href="#InputData_0ludok8" />
    </informationRequirement>
    <informationRequirement id="InformationRequirement_171fjoh">
      <requiredInput href="#InputData_0oyewbe" />
    </informationRequirement>
    <informationRequirement id="InformationRequirement_0djdfrs">
      <requiredDecision href="#Decision_0ejdbiu" />
    </informationRequirement>
    <decisionTable id="DecisionTable_0b76k6l" hitPolicy="FIRST">
      <input id="InputClause_1laeuvn" label="Asset Class">
        <inputExpression id="LiteralExpression_0c1gofk" typeRef="string">
          <text>assetClass</text>
        </inputExpression>
      </input>
      <input id="InputClause_1e2hm3f" label="Investment style">
        <inputExpression id="LiteralExpression_1ti0l7f" typeRef="string">
          <text>investmentStyle</text>
        </inputExpression>
      </input>
      <output id="OutputClause_05gh4w8" label="Minimal PRC" name="prcMin" typeRef="string" />
      <rule id="DecisionRule_18relae">
        <inputEntry id="UnaryTests_05x2ryp">
          <text>not("Money Market Fund","Bonds Fund")</text>
        </inputEntry>
        <inputEntry id="UnaryTests_10zhisn">
          <text></text>
        </inputEntry>
        <outputEntry id="LiteralExpression_02bv78b">
          <text>2</text>
        </outputEntry>
      </rule>
      <rule id="DecisionRule_10eybv4">
        <inputEntry id="UnaryTests_0leccuz">
          <text>"Money Market Fund","Bonds Fund"</text>
        </inputEntry>
        <inputEntry id="UnaryTests_0xhlca2">
          <text>"Emedging Market"</text>
        </inputEntry>
        <outputEntry id="LiteralExpression_16vtozv">
          <text>2</text>
        </outputEntry>
      </rule>
      <rule id="DecisionRule_00gl9rt">
        <inputEntry id="UnaryTests_1vge60i">
          <text>"Equity", "Real Estate"</text>
        </inputEntry>
        <inputEntry id="UnaryTests_03yaykb">
          <text></text>
        </inputEntry>
        <outputEntry id="LiteralExpression_1ry9bj7">
          <text>3</text>
        </outputEntry>
      </rule>
      <rule id="DecisionRule_0rcqaqs">
        <inputEntry id="UnaryTests_11j3a95">
          <text>"Portfolio Fund"</text>
        </inputEntry>
        <inputEntry id="UnaryTests_0axd022">
          <text>"Asset Allocation Cautious","Asset Allocation Income","Asset Allocation Unconstrained","Capital Protected","Guranted"</text>
        </inputEntry>
        <outputEntry id="LiteralExpression_187ms84">
          <text>2</text>
        </outputEntry>
      </rule>
      <rule id="DecisionRule_1olitam">
        <inputEntry id="UnaryTests_03t07wj">
          <text>"Portfolio Fund"</text>
        </inputEntry>
        <inputEntry id="UnaryTests_0ovayl2">
          <text></text>
        </inputEntry>
        <outputEntry id="LiteralExpression_1jq9yje">
          <text>3</text>
        </outputEntry>
      </rule>
      <rule id="DecisionRule_11srbgf">
        <inputEntry id="UnaryTests_01yxynl">
          <text>"Bond Fund"</text>
        </inputEntry>
        <inputEntry id="UnaryTests_1n1zskv">
          <text>"Subordinated Debt - Hybrid Capital"</text>
        </inputEntry>
        <outputEntry id="LiteralExpression_1dkrs0l">
          <text>3</text>
        </outputEntry>
      </rule>
      <rule id="DecisionRule_17t13m0">
        <inputEntry id="UnaryTests_0oemfjg">
          <text></text>
        </inputEntry>
        <inputEntry id="UnaryTests_1ashcif">
          <text></text>
        </inputEntry>
        <outputEntry id="LiteralExpression_1qxrfx5">
          <text></text>
        </outputEntry>
      </rule>
    </decisionTable>
  </decision>
  <inputData id="InputData_0ludok8" name="Investment style" />
  <inputData id="InputData_0oyewbe" name="Asset class" />
  <dmndi:DMNDI>
    <dmndi:DMNDiagram>
      <dmndi:DMNShape id="DMNShape_0tprfmw" dmnElementRef="residualDuration">
        <dc:Bounds height="45" width="125" x="1327" y="347" />
      </dmndi:DMNShape>
      <dmndi:DMNShape id="DMNShape_0erj794" dmnElementRef="rating">
        <dc:Bounds height="45" width="125" x="827" y="107" />
      </dmndi:DMNShape>
      <dmndi:DMNShape id="DMNShape_0ytws86" dmnElementRef="Decision_0d56nu3">
        <dc:Bounds height="80" width="180" x="1270" y="440" />
      </dmndi:DMNShape>
      <dmndi:DMNShape id="DMNShape_0fofa68" dmnElementRef="Decision_160rtr2">
        <dc:Bounds height="80" width="180" x="800" y="200" />
      </dmndi:DMNShape>
      <dmndi:DMNEdge id="DMNEdge_1vvndi1" dmnElementRef="InformationRequirement_15lafyx">
        <di:waypoint x="890" y="152" />
        <di:waypoint x="890" y="180" />
        <di:waypoint x="890" y="200" />
      </dmndi:DMNEdge>
      <dmndi:DMNShape id="DMNShape_0vscfpf" dmnElementRef="InputData_0imjga5">
        <dc:Bounds height="45" width="125" x="157" y="378" />
      </dmndi:DMNShape>
      <dmndi:DMNShape id="DMNShape_0q6vmvf" dmnElementRef="Decision_0ejdbiu">
        <dc:Bounds height="80" width="180" x="440" y="361" />
      </dmndi:DMNShape>
      <dmndi:DMNEdge id="DMNEdge_1dr1aps" dmnElementRef="InformationRequirement_0j6eu27">
        <di:waypoint x="282" y="401" />
        <di:waypoint x="420" y="401" />
        <di:waypoint x="440" y="401" />
      </dmndi:DMNEdge>
      <dmndi:DMNEdge id="DMNEdge_1sos3eb" dmnElementRef="InformationRequirement_04wqpii">
        <di:waypoint x="530" y="361" />
        <di:waypoint x="530" y="300" />
        <di:waypoint x="890" y="300" />
        <di:waypoint x="890" y="280" />
      </dmndi:DMNEdge>
      <dmndi:DMNEdge id="DMNEdge_0nuenj0" dmnElementRef="InformationRequirement_0fyesmb">
        <di:waypoint x="620" y="401" />
        <di:waypoint x="650" y="480" />
        <di:waypoint x="800" y="480" />
      </dmndi:DMNEdge>
      <dmndi:DMNShape id="DMNShape_0p05ki5" dmnElementRef="Decision_1x3ug27">
        <dc:Bounds height="80" width="180" x="800" y="440" />
      </dmndi:DMNShape>
      <dmndi:DMNShape id="DMNShape_1u44fkk" dmnElementRef="InputData_0jol6ae">
        <dc:Bounds height="45" width="125" x="777" y="347" />
      </dmndi:DMNShape>
      <dmndi:DMNEdge id="DMNEdge_101ayyw" dmnElementRef="InformationRequirement_0unvpqw">
        <di:waypoint x="1390" y="392" />
        <di:waypoint x="1390" y="420" />
        <di:waypoint x="1390" y="440" />
      </dmndi:DMNEdge>
      <dmndi:DMNEdge id="DMNEdge_0rm64xo" dmnElementRef="InformationRequirement_1t4wsgj">
        <di:waypoint x="890" y="280" />
        <di:waypoint x="1250" y="310" />
        <di:waypoint x="1330" y="420" />
        <di:waypoint x="1330" y="440" />
      </dmndi:DMNEdge>
      <dmndi:DMNEdge id="DMNEdge_1pu7sc1" dmnElementRef="InformationRequirement_1ayu0fc">
        <di:waypoint x="980" y="480" />
        <di:waypoint x="1250" y="480" />
        <di:waypoint x="1270" y="480" />
      </dmndi:DMNEdge>
      <dmndi:DMNEdge id="DMNEdge_1lwl97h" dmnElementRef="InformationRequirement_1q4pxoy">
        <di:waypoint x="840" y="392" />
        <di:waypoint x="860" y="420" />
        <di:waypoint x="860" y="440" />
      </dmndi:DMNEdge>
      <dmndi:DMNShape id="DMNShape_0l9s1r3" dmnElementRef="InputData_0m7cyzn">
        <dc:Bounds height="45" width="125" x="938" y="348" />
      </dmndi:DMNShape>
      <dmndi:DMNEdge id="DMNEdge_00pn50w" dmnElementRef="InformationRequirement_1fv5tii">
        <di:waypoint x="1001" y="393" />
        <di:waypoint x="920" y="420" />
        <di:waypoint x="920" y="440" />
      </dmndi:DMNEdge>
      <dmndi:DMNShape id="DMNShape_0yeucyc" dmnElementRef="Decision_16uyacj">
        <dc:Bounds height="80" width="180" x="800" y="620" />
      </dmndi:DMNShape>
      <dmndi:DMNShape id="DMNShape_1c8j2dm" dmnElementRef="InputData_0ludok8">
        <dc:Bounds height="45" width="125" x="888" y="548" />
      </dmndi:DMNShape>
      <dmndi:DMNEdge id="DMNEdge_06ktu0v" dmnElementRef="InformationRequirement_0oh3yhy">
        <di:waypoint x="951" y="593" />
        <di:waypoint x="935" y="600" />
        <di:waypoint x="935" y="620" />
      </dmndi:DMNEdge>
      <dmndi:DMNShape id="DMNShape_09pj9ky" dmnElementRef="InputData_0oyewbe">
        <dc:Bounds height="45" width="125" x="738" y="549" />
      </dmndi:DMNShape>
      <dmndi:DMNEdge id="DMNEdge_0jo2ni8" dmnElementRef="InformationRequirement_171fjoh">
        <di:waypoint x="801" y="594" />
        <di:waypoint x="890" y="600" />
        <di:waypoint x="890" y="620" />
      </dmndi:DMNEdge>
      <dmndi:DMNEdge id="DMNEdge_0anhjoz" dmnElementRef="InformationRequirement_0p29tjh">
        <di:waypoint x="980" y="622" />
        <di:waypoint x="1360" y="630" />
        <di:waypoint x="1360" y="520" />
      </dmndi:DMNEdge>
      <dmndi:DMNEdge id="DMNEdge_1gcq7n1" dmnElementRef="InformationRequirement_0djdfrs">
        <di:waypoint x="530" y="441" />
        <di:waypoint x="530" y="620" />
        <di:waypoint x="800" y="620" />
      </dmndi:DMNEdge>
    </dmndi:DMNDiagram>
  </dmndi:DMNDI>
</definitions>
`;

onMounted(async () => {
  dmnModeler = new DmnModeler({
    container: modelerContainer.value,
    width: '100%',
    height: '600px',
  });

  dmnModeler.importXML(diagram, function (err) {
    if (err) {
      return console.error('could not import DMN diagram', err);
    }

    const activeEditor = dmnModeler.getActiveViewer();
    const canvas = activeEditor.get('canvas');
    canvas.zoom('fit-viewport');
  });
});

onBeforeUnmount(() => {
  if (dmnModeler) {
    dmnModeler.destroy();
  }
});
</script>

<style scoped>
.dmn-container {
  border: 1px solid #000;
  width: 100%;
  height: 600px;
}
</style>
