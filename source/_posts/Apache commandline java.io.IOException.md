---
title: Apache commandline java.io.IOException
date: 2019-12-16 15:43:20
tags:
- java
- apache
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1.报错代码

```java
import org.apache.commons.exec.CommandLine;
import org.apache.commons.exec.DefaultExecutor;
import org.apache.commons.exec.PumpStreamHandler;
 
import java.io.*;
 
public class ApacheCommandLineTest {
    public static void main(String[] args) {
        String cmd = "source /opt/app/app.cfg";
        CommandLine commandline = new CommandLine(cmd);
        FileOutputStream fileOutputStream = null;
        try {
            fileOutputStream = new FileOutputStream("test.log", true);
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        }
        PumpStreamHandler streamHandler = new PumpStreamHandler(fileOutputStream, fileOutputStream, null);
 
        DefaultExecutor exec = new DefaultExecutor();
        exec.setExitValues(null);
        exec.setStreamHandler(streamHandler);
        try {
            int exitValue = exec.execute(commandline);  // exit code: 0=success, 1=error
            System.out.println(exitValue);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

2.异常信息

```java
java.io.IOException: Cannot run program "source \opt\app\app.cfg" (in directory "."): CreateProcess error=2, The system cannot find the file specified
	at java.lang.ProcessBuilder.start(ProcessBuilder.java:1048)
	at java.lang.Runtime.exec(Runtime.java:620)
	at org.apache.commons.exec.launcher.Java13CommandLauncher.exec(Java13CommandLauncher.java:61)
	at org.apache.commons.exec.DefaultExecutor.launch(DefaultExecutor.java:279)
	at org.apache.commons.exec.DefaultExecutor.executeInternal(DefaultExecutor.java:336)
	at org.apache.commons.exec.DefaultExecutor.execute(DefaultExecutor.java:166)
	at org.apache.commons.exec.DefaultExecutor.execute(DefaultExecutor.java:153)
	at com.xxl.job.core.handler.impl.ApacheCommandLineTest.main(ApacheCommandLineTest.java:36)
Caused by: java.io.IOException: CreateProcess error=2, The system cannot find the file specified
	at java.lang.ProcessImpl.create(Native Method)
	at java.lang.ProcessImpl.<init>(ProcessImpl.java:386)
	at java.lang.ProcessImpl.start(ProcessImpl.java:137)
	at java.lang.ProcessBuilder.start(ProcessBuilder.java:1029)
	... 7 more
```

3.改为执行脚本文件，新建脚本文件test.sh

```bash
#!/bin/bash
source /opt/app/app.cfg
/opt/venv/bin/python /opt/app/test.py
echo "End!"
```

```java
    public static void main(String[] args) {
        CommandLine commandline1 = CommandLine.parse("/bin/bash " + "test.sh");
        FileOutputStream fileOutputStream = null;
        try {
            fileOutputStream = new FileOutputStream("test.log", true);
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        }
        PumpStreamHandler streamHandler = new PumpStreamHandler(fileOutputStream, fileOutputStream, null);
 
        // exec
        DefaultExecutor exec = new DefaultExecutor();
        exec.setExitValues(null);
        exec.setStreamHandler(streamHandler);
        try {
            int exitValue1 = exec.execute(commandline1);  // exit code: 0=success, 1=error
            System.out.println(exitValue1);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
```

4.参考（https://www.programcreek.com/java-api-examples/?code=kinow/commons-sandbox/commons-sandbox-master/src/main/java/br/eti/kinoshita/commons/exec/Tests.java）

(使用命令行方式执行无论怎么修改，一直遇到这个错误，无奈使用执行脚本的方式)
