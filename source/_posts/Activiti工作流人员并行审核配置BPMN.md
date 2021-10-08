---
title: Activiti工作流人员并行审核配置BPMN
date: 2018-09-06 14:47:41
tags:
- activiti
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 任务节点配置

```xml
    <userTask id="xd_lxhsh" name="立项会审核" activiti:assignee="${assignee}" activiti:formKey="/crd/busiAudit/loan/loanProjectAudit">
      <documentation>立项会审核节点审核，当审核人数过半则通过</documentation>
      <multiInstanceLoopCharacteristics isSequential="false" activiti:collection="lxhshList" activiti:elementVariable="assignee" />
    </userTask>
```

# 后台代码

```java
Map<String,Object> params = new HashMap<>();
List<String> lxhAssigneeList = userDao.findUsersByRole("Lxhsh");
params.put("lxhshList", lxhAssigneeList);
taskService.complete(taskId, params);
```

说明：

（1）该任务节点是多人并行审核

（2）collection节点的值lxhshList是从后台传递的一个流程角色，其本身是一个Java List<String>集合，存放的是登陆人的用户名

（3）formKey指向一个请求，通过这个请求返回一个审核页面视图

（4）${assignee}默认会遍历lxhshList这个集合，取出每一个审核人

# 附录一个完整的流程，带规则引擎

```xml
<?xml version='1.0' encoding='UTF-8'?>
<definitions xmlns="http://www.omg.org/spec/BPMN/20100524/MODEL" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:activiti="http://activiti.org/bpmn" xmlns:bpmndi="http://www.omg.org/spec/BPMN/20100524/DI" xmlns:omgdc="http://www.omg.org/spec/DD/20100524/DC" xmlns:omgdi="http://www.omg.org/spec/DD/20100524/DI" typeLanguage="http://www.w3.org/2001/XMLSchema" expressionLanguage="http://www.w3.org/1999/XPath" targetNamespace="com.autoserve.weijinrong.act">
  <process id="loan_mainXdProcess" name="小贷项目审核流程" isExecutable="true">
    <startEvent id="sid-BCBE411A-AB34-421F-88AA-D18065E55177" name="开始" />
    <endEvent id="sid-45ADBD7B-2DDD-4398-8001-601C3B985F94" name="结束" />
    <userTask id="xd_ywsh" name="上级业务审核" activiti:assignee="${superLeaderAudit}" activiti:formKey="/crd/busiAudit/loan/loanProjectAudit">
      <documentation>小贷项目审核流程，业务审核节点，业务经理审核，</documentation>
    </userTask>
    <userTask id="xd_lxhsh" name="立项会审核" activiti:assignee="${assignee}" activiti:formKey="/crd/busiAudit/loan/loanProjectAudit">
      <documentation>立项会审核节点审核，当审核人数过半则通过</documentation>
      <multiInstanceLoopCharacteristics isSequential="false" activiti:collection="lxhshList" activiti:elementVariable="assignee" />
    </userTask>
    <sequenceFlow id="sid-7FBF2312-615F-45F9-A940-51EDD7A86C3E" sourceRef="xd_ywsh" targetRef="xd_lxhsh" />
    <userTask id="xd_dshsh" name="贷审会审核" activiti:assignee="${assignee}" activiti:formKey="/crd/busiAudit/loan/loanProjectAudit">
      <documentation>小贷贷审会审核</documentation>
      <multiInstanceLoopCharacteristics isSequential="false" activiti:collection="dshshList" activiti:elementVariable="assignee" />
    </userTask>
    <userTask id="xd_lxhtjls" name="客户经理条件落实" activiti:assignee="${customerManager}" activiti:formKey="/crd/busiAudit/loan/loanProjectAudit">
      <documentation>该项目客户经理条件落实</documentation>
    </userTask>
    <userTask id="xd_lxhfh" name="上级复核" activiti:assignee="${superLeaderAudit}" activiti:formKey="/crd/busiAudit/loan/loanProjectAudit">
      <documentation>客户经理直接上级复议</documentation>
    </userTask>
    <sequenceFlow id="sid-C8D4BDE3-BE8B-472A-8143-D29A5F422CA6" sourceRef="xd_lxhtjls" targetRef="xd_lxhfh" />
    <exclusiveGateway id="xd_lxhshfz" name="是否通过">
      <documentation>小贷立项会审核分支</documentation>
    </exclusiveGateway>
    <userTask id="xd_jd" name="客户经理尽调" activiti:assignee="${customerManager}" activiti:formKey="/crd/busiAudit/loan/loanProjectAudit" />
    <exclusiveGateway id="sid-CFD73D16-29B8-429E-866E-D4401EC0515D" name="是否通过" />
    <exclusiveGateway id="xd_dshshfz" name="是否通过" />
    <sequenceFlow id="sid-5BF156BD-FD03-4FB5-8153-50200F342269" sourceRef="xd_dshsh" targetRef="xd_dshshfz" />
    <userTask id="xd_dshtjls" name="客户经理条件落实" activiti:assignee="${customerManager}" activiti:formKey="/crd/busiAudit/loan/loanProjectAudit">
      <documentation>该项目客户经理条件落实</documentation>
    </userTask>
    <userTask id="xd_dshfh" name="上级复核" activiti:assignee="${superLeaderManager}" activiti:formKey="/crd/busiAudit/loan/loanProjectAudit">
      <documentation>客户经理直接上级复议</documentation>
    </userTask>
    <sequenceFlow id="sid-DCA50B9B-E742-4169-8C06-595036977E15" sourceRef="xd_dshtjls" targetRef="xd_dshfh" />
    <exclusiveGateway id="sid-F77483BC-ED96-49C5-A282-112069DEE4FB" name="是否通过" />
    <sequenceFlow id="sid-47B6A5F4-E22D-4515-8BB1-B0B36058E169" sourceRef="xd_dshfh" targetRef="sid-F77483BC-ED96-49C5-A282-112069DEE4FB" />
    <userTask id="xd_edqr" name="额度确认" activiti:assignee="${customerManager}" activiti:formKey="/crd/busiAudit/loan/loanProjectAudit" />
    <sequenceFlow id="sid-E1BBEB61-8FFF-40E3-BAC4-8D6994AA363B" sourceRef="xd_lxhsh" targetRef="xd_lxhshfz" />
    <sequenceFlow id="sid-037B9012-B751-4420-AC61-A8550B9F9D9F" sourceRef="xd_edqr" targetRef="sid-45ADBD7B-2DDD-4398-8001-601C3B985F94" />
    <userTask id="xd_gbmyj" name="各部门意见" activiti:assignee="${assignee}" activiti:formKey="/crd/busiAudit/loan/loanProjectAudit">
      <documentation>还是立项会成员</documentation>
      <multiInstanceLoopCharacteristics isSequential="false" activiti:collection="lxhshList" activiti:elementVariable="assignee" />
    </userTask>
    <sequenceFlow id="sid-75CCB2CD-E964-49DC-ADEA-6909FEA3E5A1" sourceRef="xd_jd" targetRef="xd_gbmyj" />
    <sequenceFlow id="sid-16E7B369-3910-4EBB-9BF9-CE0B225E1AE9" sourceRef="xd_gbmyj" targetRef="xd_dshsh" />
    <sequenceFlow id="sid-8E5ACB55-FBD3-44C4-B037-EC5314D4CD01" name="条件" sourceRef="xd_lxhshfz" targetRef="xd_lxhtjls">
      <conditionExpression xsi:type="tFormalExpression"><![CDATA[${passLabel==0}]]></conditionExpression>
    </sequenceFlow>
    <sequenceFlow id="sid-4C81A4F4-67D9-4448-8EB7-236BEF04AC84" sourceRef="xd_lxhfh" targetRef="sid-CFD73D16-29B8-429E-866E-D4401EC0515D" />
    <sequenceFlow id="sid-A9552292-7B77-4A5F-9D39-26A7AFD3EC00" name="未通过" sourceRef="sid-CFD73D16-29B8-429E-866E-D4401EC0515D" targetRef="xd_lxhtjls">
      <conditionExpression xsi:type="tFormalExpression"><![CDATA[${passLabel==0}]]></conditionExpression>
    </sequenceFlow>
    <sequenceFlow id="sid-F9A2116D-906E-4BE7-AA24-F6196EBEE17A" name="通过" sourceRef="sid-CFD73D16-29B8-429E-866E-D4401EC0515D" targetRef="xd_jd">
      <conditionExpression xsi:type="tFormalExpression"><![CDATA[${passLabel==1}]]></conditionExpression>
    </sequenceFlow>
    <sequenceFlow id="sid-1852ED22-9FF3-4A55-A053-B59C0E07E677" name="通过" sourceRef="sid-F77483BC-ED96-49C5-A282-112069DEE4FB" targetRef="xd_edqr">
      <conditionExpression xsi:type="tFormalExpression"><![CDATA[${passLabel==1}]]></conditionExpression>
    </sequenceFlow>
    <sequenceFlow id="sid-A13C300C-9D95-4D00-9C23-A27BD6347819" name="未通过" sourceRef="sid-F77483BC-ED96-49C5-A282-112069DEE4FB" targetRef="xd_dshtjls">
      <conditionExpression xsi:type="tFormalExpression"><![CDATA[${passLabel==0}]]></conditionExpression>
    </sequenceFlow>
    <sequenceFlow id="sid-89815E66-A6C0-4221-B9A3-1A22BAA97E73" name="条件" sourceRef="xd_dshshfz" targetRef="xd_dshtjls">
      <conditionExpression xsi:type="tFormalExpression"><![CDATA[${passLabel==0}]]></conditionExpression>
    </sequenceFlow>
    <sequenceFlow id="sid-A0A2200B-D4FE-439D-9B4E-355BAFD19721" name="通过" sourceRef="xd_dshshfz" targetRef="xd_edqr">
      <conditionExpression xsi:type="tFormalExpression"><![CDATA[${passLabel==1}]]></conditionExpression>
    </sequenceFlow>
    <businessRuleTask id="inside-policy" name="内部流程规则" activiti:ruleVariablesInput="${riskruleinput}" activiti:resultVariable="resultmap">
      <extensionElements>
        <activiti:executionListener event="start" class="com.autoserve.weijinrong.modules.act.listen.RuleTaskStartListen" />
        <activiti:executionListener event="end" class="com.autoserve.weijinrong.modules.act.listen.RuleTaskEndListen" />
      </extensionElements>
    </businessRuleTask>
    <sequenceFlow id="sid-282149F4-A1D5-4FCE-9B5D-CB8DF26144FE" sourceRef="sid-BCBE411A-AB34-421F-88AA-D18065E55177" targetRef="inside-policy" />
    <exclusiveGateway id="sid-29047CFB-7E04-458A-BFBC-2021362B364C" />
    <sequenceFlow id="sid-B413E7CE-5B1B-4768-8B1B-0B8D709AE6A1" sourceRef="inside-policy" targetRef="sid-29047CFB-7E04-458A-BFBC-2021362B364C" />
    <sequenceFlow id="sid-A670BBAC-9215-4779-B60D-81D246357FB8" name="审核未通过或无需审核" sourceRef="sid-29047CFB-7E04-458A-BFBC-2021362B364C" targetRef="sid-45ADBD7B-2DDD-4398-8001-601C3B985F94">
      <conditionExpression xsi:type="tFormalExpression"><![CDATA[${resultmap[0].whereabouts!= '审核通过'}]]></conditionExpression>
    </sequenceFlow>
    <sequenceFlow id="sid-DBCB5579-18D6-4E1C-A3FE-C5E50EA35871" name="审核通过" sourceRef="sid-29047CFB-7E04-458A-BFBC-2021362B364C" targetRef="xd_ywsh">
      <conditionExpression xsi:type="tFormalExpression"><![CDATA[${resultmap[0].whereabouts== '审核通过'}]]></conditionExpression>
    </sequenceFlow>
    <sequenceFlow id="sid-38063097-2E15-46FB-8660-CBA2C7B1590C" name="通过" sourceRef="xd_lxhshfz" targetRef="xd_jd">
      <conditionExpression xsi:type="tFormalExpression"><![CDATA[${passLabel==1}]]></conditionExpression>
    </sequenceFlow>
  </process>
  <bpmndi:BPMNDiagram id="BPMNDiagram_loan_mainXdProcess">
    <bpmndi:BPMNPlane bpmnElement="loan_mainXdProcess" id="BPMNPlane_loan_mainXdProcess">
      <bpmndi:BPMNShape bpmnElement="sid-BCBE411A-AB34-421F-88AA-D18065E55177" id="BPMNShape_sid-BCBE411A-AB34-421F-88AA-D18065E55177">
        <omgdc:Bounds height="30.0" width="30.0" x="75.0" y="100.0" />
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="sid-45ADBD7B-2DDD-4398-8001-601C3B985F94" id="BPMNShape_sid-45ADBD7B-2DDD-4398-8001-601C3B985F94">
        <omgdc:Bounds height="28.0" width="28.0" x="1245.9999582767502" y="218.0" />
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="xd_ywsh" id="BPMNShape_xd_ywsh">
        <omgdc:Bounds height="80.0" width="100.0" x="99.99999602635717" y="315.0" />
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="xd_lxhsh" id="BPMNShape_xd_lxhsh">
        <omgdc:Bounds height="80.0" width="99.99999999999997" x="249.99999006589294" y="315.0" />
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="xd_dshsh" id="BPMNShape_xd_dshsh">
        <omgdc:Bounds height="80.0" width="100.0" x="909.9999701976787" y="315.0" />
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="xd_lxhtjls" id="BPMNShape_xd_lxhtjls">
        <omgdc:Bounds height="80.00000000000011" width="100.0" x="383.33331810103584" y="483.333314127393" />
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="xd_lxhfh" id="BPMNShape_xd_lxhfh">
        <omgdc:Bounds height="79.99999999999989" width="100.0" x="383.33331810103584" y="643.333314127393" />
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="xd_lxhshfz" id="BPMNShape_xd_lxhshfz">
        <omgdc:Bounds height="40.0" width="40.0" x="413.33331810103584" y="335.0" />
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="xd_jd" id="BPMNShape_xd_jd">
        <omgdc:Bounds height="80.0" width="100.0" x="583.3333101537502" y="315.0" />
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="sid-CFD73D16-29B8-429E-866E-D4401EC0515D" id="BPMNShape_sid-CFD73D16-29B8-429E-866E-D4401EC0515D">
        <omgdc:Bounds height="40.0" width="40.0" x="613.3333101537502" y="663.3333141273929" />
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="xd_dshshfz" id="BPMNShape_xd_dshshfz">
        <omgdc:Bounds height="40.0" width="40.0" x="1072.2221851790407" y="335.0" />
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="xd_dshtjls" id="BPMNShape_xd_dshtjls">
        <omgdc:Bounds height="80.0" width="100.0" x="1043.3332982328216" y="466.6666481230002" />
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="xd_dshfh" id="BPMNShape_xd_dshfh">
        <omgdc:Bounds height="79.99999999999977" width="100.0" x="1043.3332982328216" y="629.9999821186072" />
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="sid-F77483BC-ED96-49C5-A282-112069DEE4FB" id="BPMNShape_sid-F77483BC-ED96-49C5-A282-112069DEE4FB">
        <omgdc:Bounds height="40.0" width="40.0" x="1243.3332902855361" y="649.9999821186072" />
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="xd_edqr" id="BPMNShape_xd_edqr">
        <omgdc:Bounds height="80.0" width="100.0" x="1209.9999582767502" y="315.0" />
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="xd_gbmyj" id="BPMNShape_xd_gbmyj">
        <omgdc:Bounds height="80.0" width="100.0" x="750.0" y="315.0" />
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="inside-policy" id="BPMNShape_inside-policy">
        <omgdc:Bounds height="80.0" width="100.0" x="383.33331810103584" y="75.0" />
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="sid-29047CFB-7E04-458A-BFBC-2021362B364C" id="BPMNShape_sid-29047CFB-7E04-458A-BFBC-2021362B364C">
        <omgdc:Bounds height="40.0" width="40.0" x="413.33331810103584" y="212.0" />
      </bpmndi:BPMNShape>
      <bpmndi:BPMNEdge bpmnElement="sid-16E7B369-3910-4EBB-9BF9-CE0B225E1AE9" id="BPMNEdge_sid-16E7B369-3910-4EBB-9BF9-CE0B225E1AE9">
        <omgdi:waypoint x="850.0" y="355.0" />
        <omgdi:waypoint x="909.9999701976787" y="355.0" />
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="sid-47B6A5F4-E22D-4515-8BB1-B0B36058E169" id="BPMNEdge_sid-47B6A5F4-E22D-4515-8BB1-B0B36058E169">
        <omgdi:waypoint x="1143.3332982328216" y="669.9999821186071" />
        <omgdi:waypoint x="1243.3332902855361" y="669.9999821186072" />
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="sid-282149F4-A1D5-4FCE-9B5D-CB8DF26144FE" id="BPMNEdge_sid-282149F4-A1D5-4FCE-9B5D-CB8DF26144FE">
        <omgdi:waypoint x="105.0" y="115.0" />
        <omgdi:waypoint x="383.33331810103584" y="115.0" />
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="sid-E1BBEB61-8FFF-40E3-BAC4-8D6994AA363B" id="BPMNEdge_sid-E1BBEB61-8FFF-40E3-BAC4-8D6994AA363B">
        <omgdi:waypoint x="349.9999900658929" y="355.0" />
        <omgdi:waypoint x="413.33331810103584" y="355.0" />
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="sid-4C81A4F4-67D9-4448-8EB7-236BEF04AC84" id="BPMNEdge_sid-4C81A4F4-67D9-4448-8EB7-236BEF04AC84">
        <omgdi:waypoint x="483.33331810103584" y="683.3333141273929" />
        <omgdi:waypoint x="613.3333101537502" y="683.3333141273929" />
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="sid-DBCB5579-18D6-4E1C-A3FE-C5E50EA35871" id="BPMNEdge_sid-DBCB5579-18D6-4E1C-A3FE-C5E50EA35871">
        <omgdi:waypoint x="418.379597732211" y="237.0462796311751" />
        <omgdi:waypoint x="153.39919748832938" y="315.0" />
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="sid-7FBF2312-615F-45F9-A940-51EDD7A86C3E" id="BPMNEdge_sid-7FBF2312-615F-45F9-A940-51EDD7A86C3E">
        <omgdi:waypoint x="199.99999602635717" y="355.0" />
        <omgdi:waypoint x="249.99999006589294" y="355.0" />
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="sid-DCA50B9B-E742-4169-8C06-595036977E15" id="BPMNEdge_sid-DCA50B9B-E742-4169-8C06-595036977E15">
        <omgdi:waypoint x="1093.3332982328216" y="546.6666481230002" />
        <omgdi:waypoint x="1093.3332982328216" y="629.9999821186072" />
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="sid-B413E7CE-5B1B-4768-8B1B-0B8D709AE6A1" id="BPMNEdge_sid-B413E7CE-5B1B-4768-8B1B-0B8D709AE6A1">
        <omgdi:waypoint x="433.33331810103584" y="155.0" />
        <omgdi:waypoint x="433.33331810103584" y="212.0" />
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="sid-037B9012-B751-4420-AC61-A8550B9F9D9F" id="BPMNEdge_sid-037B9012-B751-4420-AC61-A8550B9F9D9F">
        <omgdi:waypoint x="1259.9999582767502" y="315.0" />
        <omgdi:waypoint x="1259.9999582767502" y="246.0" />
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="sid-A0A2200B-D4FE-439D-9B4E-355BAFD19721" id="BPMNEdge_sid-A0A2200B-D4FE-439D-9B4E-355BAFD19721">
        <omgdi:waypoint x="1112.2221851790407" y="355.0" />
        <omgdi:waypoint x="1209.9999582767502" y="355.0" />
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="sid-5BF156BD-FD03-4FB5-8153-50200F342269" id="BPMNEdge_sid-5BF156BD-FD03-4FB5-8153-50200F342269">
        <omgdi:waypoint x="1009.9999701976787" y="355.0" />
        <omgdi:waypoint x="1072.2221851790407" y="355.0" />
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="sid-A13C300C-9D95-4D00-9C23-A27BD6347819" id="BPMNEdge_sid-A13C300C-9D95-4D00-9C23-A27BD6347819">
        <omgdi:waypoint x="1253.133290539452" y="660.1999818646915" />
        <omgdi:waypoint x="1134.9659491789625" y="546.6666481230002" />
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="sid-75CCB2CD-E964-49DC-ADEA-6909FEA3E5A1" id="BPMNEdge_sid-75CCB2CD-E964-49DC-ADEA-6909FEA3E5A1">
        <omgdi:waypoint x="683.3333101537502" y="355.0" />
        <omgdi:waypoint x="750.0" y="355.0" />
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="sid-1852ED22-9FF3-4A55-A053-B59C0E07E677" id="BPMNEdge_sid-1852ED22-9FF3-4A55-A053-B59C0E07E677">
        <omgdi:waypoint x="1263.123866272349" y="650.2094061317944" />
        <omgdi:waypoint x="1260.4232385558623" y="395.0" />
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="sid-8E5ACB55-FBD3-44C4-B037-EC5314D4CD01" id="BPMNEdge_sid-8E5ACB55-FBD3-44C4-B037-EC5314D4CD01">
        <omgdi:waypoint x="433.33331810103584" y="375.0" />
        <omgdi:waypoint x="433.33331810103584" y="483.333314127393" />
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="sid-A9552292-7B77-4A5F-9D39-26A7AFD3EC00" id="BPMNEdge_sid-A9552292-7B77-4A5F-9D39-26A7AFD3EC00">
        <omgdi:waypoint x="622.2221992388684" y="674.4444250422747" />
        <omgdi:waypoint x="483.3333161142145" y="563.3333141273931" />
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="sid-C8D4BDE3-BE8B-472A-8143-D29A5F422CA6" id="BPMNEdge_sid-C8D4BDE3-BE8B-472A-8143-D29A5F422CA6">
        <omgdi:waypoint x="433.33331810103584" y="563.3333141273931" />
        <omgdi:waypoint x="433.33331810103584" y="643.333314127393" />
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="sid-A670BBAC-9215-4779-B60D-81D246357FB8" id="BPMNEdge_sid-A670BBAC-9215-4779-B60D-81D246357FB8">
        <omgdi:waypoint x="452.8448239552605" y="232.48849414577538" />
        <omgdi:waypoint x="1245.9999608406592" y="232.00847286537322" />
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="sid-F9A2116D-906E-4BE7-AA24-F6196EBEE17A" id="BPMNEdge_sid-F9A2116D-906E-4BE7-AA24-F6196EBEE17A">
        <omgdi:waypoint x="633.3333101537502" y="663.3333141273929" />
        <omgdi:waypoint x="633.3333101537502" y="395.0" />
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="sid-89815E66-A6C0-4221-B9A3-1A22BAA97E73" id="BPMNEdge_sid-89815E66-A6C0-4221-B9A3-1A22BAA97E73">
        <omgdi:waypoint x="1092.3676399946137" y="374.854545184427" />
        <omgdi:waypoint x="1093.0402573915999" y="466.6666481230002" />
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="sid-38063097-2E15-46FB-8660-CBA2C7B1590C" id="BPMNEdge_sid-38063097-2E15-46FB-8660-CBA2C7B1590C">
        <omgdi:waypoint x="453.33331810103584" y="355.0" />
        <omgdi:waypoint x="583.3333101537502" y="355.0" />
      </bpmndi:BPMNEdge>
    </bpmndi:BPMNPlane>
  </bpmndi:BPMNDiagram>
</definitions>
```
