---
title: Java修改文件中某一行字符串
date: 2020-01-13 15:42:38
tags:
- java
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

> 背景：想使用Java里的Apache common exec工具包中的CommandLine执行shell文件，因为shell文件里有一行是使用虚拟环境下的python命令去执行python脚本，我们想动态的修改python后面跟随的参数。

1.shell脚本（比如这里的参数是JobName1）

```shell
#!/bin/bash
source /opt/xxc/flask_config.cfg
/opt/venv/bin/python /opt/xxc/dashboard/jobs/job.py JobName1
echo "End!"
```

2.如上，我们想将JobName1改成JobName2，同时写回到这个shell脚本里，脚本名字叫run_script.sh。

```java
import java.io.*;
 
public class Test {
    public static void main(String[] args) {
        String jobName = "JobName2";
        String filePath = "/opt/xxc/run_script.sh";
        String content = readFile(filePath, jobName);
        writeFile(filePath, content);
    }
 
    private static String readFile(String filePath, String jobName){
        String cmd = "/opt/venv/bin/python /opt/xxc/dashboard/jobs/job.py";
        BufferedReader br = null;
        String line;
        StringBuffer bufAll = new StringBuffer();
        try {
            br = new BufferedReader(new FileReader(filePath));
            while ((line = br.readLine()) != null) {
                StringBuffer buf = new StringBuffer();
                if (line.startsWith(cmd)) {
                    buf.append(cmd);
                    buf.append(" ").append(jobName);
                    buf.append(System.getProperty("line.separator"));
                }else {
                    buf.append(line);
                    buf.append(System.getProperty("line.separator"));
                }
                bufAll.append(buf);
            }
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            if (br != null) {
                try {
                    br.close();
                }catch (IOException e){
                    br = null;
                }
            }
        }
        return bufAll.toString();
    }
 
    private static void writeFile(String filePath, String content) {
        BufferedWriter bw = null;
        try {
            bw = new BufferedWriter(new FileWriter(filePath));
            bw.write(content);
        }catch (Exception e) {
            e.printStackTrace();
        }finally {
            if (bw != null){
                try{
                    bw.close();
                }catch (IOException e) {
                    bw = null;
                }
            }
        }
    }
}
```

3.上面的方法是先去读shell脚本，读的同时修改那一行代码，然后把shell内容重新组装成一个字符串，最后再塞到原文件中。执行后的结果是

```shell
#!/bin/bash
source /opt/xxc/flask_config.cfg
/opt/venv/bin/python /opt/xxc/dashboard/jobs/job.py JobName2
echo "End!"
```

参考：

1. https://blog.csdn.net/qq_33643690/article/details/81737957  （转载）

2. https://blog.csdn.net/silentwolfyh/article/details/74939703  （Java的RandomAccessFile类实现，不太会用）

3. https://blog.csdn.net/weixin_35653315/article/details/72886718 （python传递参数的三种方式）

4. https://blog.csdn.net/f45056231p/article/details/88692444  （java获取路径）

5. https://blog.csdn.net/qq_36769100/article/details/79373269  （java获取系统路径分隔符）
