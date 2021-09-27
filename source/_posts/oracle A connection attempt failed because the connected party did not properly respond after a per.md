---
title: oracle A connection attempt failed because the connected party did not properly respond after a per
date: 2020-07-28 15:20:21
tags:
- oracle
categories:
---

## 报错信息

```
05:43:09.548 logback [http-nio-9996-exec-2] ERROR c.alibaba.druid.pool.DruidDataSource - discard connection
java.sql.SQLRecoverableException: IO Error: A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond
	at oracle.jdbc.driver.T4CPreparedStatement.executeForDescribe(T4CPreparedStatement.java:821)
	at oracle.jdbc.driver.OracleStatement.executeMaybeDescribe(OracleStatement.java:983)
	at oracle.jdbc.driver.OracleStatement.doExecuteWithTimeout(OracleStatement.java:1168)
	at oracle.jdbc.driver.OraclePreparedStatement.executeInternal(OraclePreparedStatement.java:3666)
	at oracle.jdbc.driver.T4CPreparedStatement.executeInternal(T4CPreparedStatement.java:1426)
	at oracle.jdbc.driver.OraclePreparedStatement.execute(OraclePreparedStatement.java:3778)
	at oracle.jdbc.driver.OraclePreparedStatementWrapper.execute(OraclePreparedStatementWrapper.java:1081)
	at com.alibaba.druid.filter.FilterChainImpl.preparedStatement_execute(FilterChainImpl.java:3409)
	at com.alibaba.druid.filter.FilterEventAdapter.preparedStatement_execute(FilterEventAdapter.java:440)
	at com.alibaba.druid.filter.FilterChainImpl.preparedStatement_execute(FilterChainImpl.java:3407)
	at com.alibaba.druid.proxy.jdbc.PreparedStatementProxyImpl.execute(PreparedStatementProxyImpl.java:167)
	at com.alibaba.druid.pool.DruidPooledPreparedStatement.execute(DruidPooledPreparedStatement.java:498)
	at org.apache.ibatis.executor.statement.PreparedStatementHandler.query(PreparedStatementHandler.java:64)
	at org.apache.ibatis.executor.statement.RoutingStatementHandler.query(RoutingStatementHandler.java:79)
	at org.apache.ibatis.executor.SimpleExecutor.doQuery(SimpleExecutor.java:63)
	at org.apache.ibatis.executor.BaseExecutor.queryFromDatabase(BaseExecutor.java:324)
	at org.apache.ibatis.executor.BaseExecutor.query(BaseExecutor.java:156)
	at org.apache.ibatis.executor.CachingExecutor.query(CachingExecutor.java:109)
	at org.apache.ibatis.executor.CachingExecutor.query(CachingExecutor.java:83)
	at org.apache.ibatis.session.defaults.DefaultSqlSession.selectList(DefaultSqlSession.java:147)
	at org.apache.ibatis.session.defaults.DefaultSqlSession.selectList(DefaultSqlSession.java:140)
	at org.apache.ibatis.session.defaults.DefaultSqlSession.selectOne(DefaultSqlSession.java:76)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at org.mybatis.spring.SqlSessionTemplate$SqlSessionInterceptor.invoke(SqlSessionTemplate.java:433)
	at com.sun.proxy.$Proxy60.selectOne(Unknown Source)
	at org.mybatis.spring.SqlSessionTemplate.selectOne(SqlSessionTemplate.java:166)
	at org.apache.ibatis.binding.MapperMethod.execute(MapperMethod.java:87)
	at org.apache.ibatis.binding.MapperProxy.invoke(MapperProxy.java:58)
	at com.sun.proxy.$Proxy63.loadByName(Unknown Source)
	at com.xxl.job.admin.service.impl.XxlJobServiceImpl.update(XxlJobServiceImpl.java:262)   ---执行update时
	at com.xxl.job.admin.service.impl.XxlJobServiceImpl$$FastClassBySpringCGLIB$$3c56498e.invoke(<generated>)
	at org.springframework.cglib.proxy.MethodProxy.invoke(MethodProxy.java:218)
	at org.springframework.aop.framework.CglibAopProxy$DynamicAdvisedInterceptor.intercept(CglibAopProxy.java:684)
	at com.xxl.job.admin.service.impl.XxlJobServiceImpl$$EnhancerBySpringCGLIB$$54bc9d8b.update(<generated>)
	at com.xxl.job.admin.controller.JobInfoController.update(JobInfoController.java:125)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at org.springframework.web.method.support.InvocableHandlerMethod.doInvoke(InvocableHandlerMethod.java:189)
	at org.springframework.web.method.support.InvocableHandlerMethod.invokeForRequest(InvocableHandlerMethod.java:138)
	at org.springframework.web.servlet.mvc.method.annotation.ServletInvocableHandlerMethod.invokeAndHandle(ServletInvocableHandlerMethod.java:102)
	at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.invokeHandlerMethod(RequestMappingHandlerAdapter.java:895)
	at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.handleInternal(RequestMappingHandlerAdapter.java:800)
	at org.springframework.web.servlet.mvc.method.AbstractHandlerMethodAdapter.handle(AbstractHandlerMethodAdapter.java:87)
	at org.springframework.web.servlet.DispatcherServlet.doDispatch(DispatcherServlet.java:1038)
	at org.springframework.web.servlet.DispatcherServlet.doService(DispatcherServlet.java:942)
	at org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:1005)
	at org.springframework.web.servlet.FrameworkServlet.doPost(FrameworkServlet.java:908)
	at javax.servlet.http.HttpServlet.service(HttpServlet.java:660)
	at org.springframework.web.servlet.FrameworkServlet.service(FrameworkServlet.java:882)
	at javax.servlet.http.HttpServlet.service(HttpServlet.java:741)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:231)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
	at org.apache.tomcat.websocket.server.WsFilter.doFilter(WsFilter.java:53)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
	at com.alibaba.druid.support.http.WebStatFilter.doFilter(WebStatFilter.java:123)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
	at org.springframework.web.filter.RequestContextFilter.doFilterInternal(RequestContextFilter.java:99)
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:107)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
	at org.springframework.web.filter.FormContentFilter.doFilterInternal(FormContentFilter.java:92)
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:107)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
	at org.springframework.web.filter.HiddenHttpMethodFilter.doFilterInternal(HiddenHttpMethodFilter.java:93)
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:107)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
	at org.springframework.web.filter.CharacterEncodingFilter.doFilterInternal(CharacterEncodingFilter.java:200)
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:107)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
	at org.apache.catalina.core.StandardWrapperValve.invoke(StandardWrapperValve.java:200)
	at org.apache.catalina.core.StandardContextValve.invoke(StandardContextValve.java:96)
	at org.apache.catalina.authenticator.AuthenticatorBase.invoke(AuthenticatorBase.java:490)
	at org.apache.catalina.core.StandardHostValve.invoke(StandardHostValve.java:139)
	at org.apache.catalina.valves.ErrorReportValve.invoke(ErrorReportValve.java:92)
	at org.apache.catalina.core.StandardEngineValve.invoke(StandardEngineValve.java:74)
	at org.apache.catalina.connector.CoyoteAdapter.service(CoyoteAdapter.java:343)
	at org.apache.coyote.http11.Http11Processor.service(Http11Processor.java:408)
	at org.apache.coyote.AbstractProcessorLight.process(AbstractProcessorLight.java:66)
	at org.apache.coyote.AbstractProtocol$ConnectionHandler.process(AbstractProtocol.java:834)
	at org.apache.tomcat.util.net.NioEndpoint$SocketProcessor.doRun(NioEndpoint.java:1415)
	at org.apache.tomcat.util.net.SocketProcessorBase.run(SocketProcessorBase.java:49)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
	at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:61)
	at java.lang.Thread.run(Thread.java:748)
Caused by: java.io.IOException: A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond
	at sun.nio.ch.SocketDispatcher.read0(Native Method)
	at sun.nio.ch.SocketDispatcher.read(SocketDispatcher.java:43)
	at sun.nio.ch.IOUtil.readIntoNativeBuffer(IOUtil.java:223)
	at sun.nio.ch.IOUtil.read(IOUtil.java:197)
	at sun.nio.ch.SocketChannelImpl.read(SocketChannelImpl.java:380)
	at oracle.net.nt.TimeoutSocketChannel.read(TimeoutSocketChannel.java:174)
	at oracle.net.ns.NSProtocolNIO.doSocketRead(NSProtocolNIO.java:555)
	at oracle.net.ns.NIOPacket.readHeader(NIOPacket.java:258)
	at oracle.net.ns.NIOPacket.readPacketFromSocketChannel(NIOPacket.java:190)
	at oracle.net.ns.NIOPacket.readFromSocketChannel(NIOPacket.java:132)
	at oracle.net.ns.NIOPacket.readFromSocketChannel(NIOPacket.java:105)
	at oracle.net.ns.NIONSDataChannel.readDataFromSocketChannel(NIONSDataChannel.java:91)
	at oracle.jdbc.driver.T4CMAREngineNIO.prepareForUnmarshall(T4CMAREngineNIO.java:764)
	at oracle.jdbc.driver.T4CMAREngineNIO.unmarshalUB1(T4CMAREngineNIO.java:429)
	at oracle.jdbc.driver.T4CTTIfun.receive(T4CTTIfun.java:407)
	at oracle.jdbc.driver.T4CTTIfun.doRPC(T4CTTIfun.java:268)
	at oracle.jdbc.driver.T4C8Oall.doOALL(T4C8Oall.java:655)
	at oracle.jdbc.driver.T4CPreparedStatement.doOall8(T4CPreparedStatement.java:270)
	at oracle.jdbc.driver.T4CPreparedStatement.doOall8(T4CPreparedStatement.java:91)
	at oracle.jdbc.driver.T4CPreparedStatement.executeForDescribe(T4CPreparedStatement.java:807)
	... 94 common frames omitted
```

## 解决：看traceback最终是在调用jdbc时出问题的，尝试修改pom中引入的ojdbc依赖

原依赖：

```xml
		<dependency>
			<groupId>com.oracle.ojdbc</groupId>
			<artifactId>ojdbc8</artifactId>
			<version>19.3.0.0</version>
		</dependency>
```

修改为：

```xml
		<dependency>
			<groupId>com.oracle</groupId>
			<artifactId>ojdbc8</artifactId>
			<version>12.2.0.1</version>
		</dependency>
```

## 说明

这个报错其实很奇怪，后端报错语句如下，很简单的一行，

并且当jobInfo.getJobDesc()为其他值时都正常，只有为某个值时会报错

> XxlJobInfo existJobInfo = xxlJobInfoDao.loadByName(jobInfo.getJobDesc());
