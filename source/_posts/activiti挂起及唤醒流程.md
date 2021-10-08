---
title: activiti挂起及唤醒流程
date: 2018-07-09 08:32:03
tags:
- activiti
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1. 挂起（锁定）/唤醒（解锁）流程

说明：通过操作流程实例ID实现

业务实现：

```java
	/**
	 * 根据一个流程实例的id挂起流程实例
	 * @param processInstanceId 流程实例id
	 */
	public void suspendProcessInstance( String processInstanceId ){
		runtimeService.suspendProcessInstanceById(processInstanceId);
	}
 
	/**
	 * 根据一个流程实例的id激活流程实例
	 * @param processInstanceId 流程实例id
	 */
	public void activateProcessInstance( String processInstanceId ){
		runtimeService.activateProcessInstanceById(processInstanceId);
	}
```

源码及注释：

```java
  /**
   * Suspends the process instance with the given id.
   * 
   * If a process instance is in state suspended, activiti will not execute jobs
   * (timers, messages) associated with this instance.
   * 
   * If you have a process instance hierarchy, suspending one process instance
   * form the hierarchy will not suspend other process instances form that
   * hierarchy.
   * 
   * @throws ActivitiObjectNotFoundException
   *           if no such processInstance can be found.
   * @throws ActivitiException
   *           the process instance is already in state suspended.
   */
  void suspendProcessInstanceById(String processInstanceId);
```

```java
  /**
   * Activates the process instance with the given id.
   * 
   * If you have a process instance hierarchy, suspending one process instance
   * form the hierarchy will not suspend other process instances form that
   * hierarchy.
   * 
   * @throws ActivitiObjectNotFoundException
   *           if no such processInstance can be found.
   * @throws ActivitiException
   *           if the process instance is already in state active.
   */
  void activateProcessInstanceById(String processInstanceId);
```

2. 启动流程

说明：通过processDefinitionKey启动流程并返回一个实例实例对象

业务实现：

```java
ProcessInstance procIns = runtimeService.startProcessInstanceByKey(procDefKey, businessKey, vars);
```

源码及注释：

```java
  /**
   * Starts a new process instance in the latest version of the process
   * definition with the given key.
   * 
   * A business key can be provided to associate the process instance with a
   * certain identifier that has a clear business meaning. For example in an
   * order process, the business key could be an order id. This business key can
   * then be used to easily look up that process instance , see
   * {@link ProcessInstanceQuery#processInstanceBusinessKey(String)}. Providing
   * such a business key is definitely a best practice.
   * 
   * Note that a business key MUST be unique for the given process definition.
   * Process instance from different process definition are allowed to have the
   * same business key.
   * 
   * The combination of processdefinitionKey-businessKey must be unique.
   * 
   * @param processDefinitionKey
   *          key of process definition, cannot be null.
   * @param variables
   *          the variables to pass, can be null.
   * @param businessKey
   *          a key that uniquely identifies the process instance in the context
   *          or the given process definition.
   * @throws ActivitiObjectNotFoundException
   *           when no process definition is deployed with the given key.
   */
  ProcessInstance startProcessInstanceByKey(String processDefinitionKey, String businessKey, Map<String, Object> variables);
```


