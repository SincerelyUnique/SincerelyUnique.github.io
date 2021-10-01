---
title: centos使用shell脚本执行python文件报No such file or directory
date: 2020-01-10 15:34:31
tags:
- linux
- shell
- python
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1.执行的脚本叫run_script.sh，脚本内容如下 

```shell
#!/bin/bash
source /opt/xxxx/xxxx.cfg
/opt/venv/bin/python /opt/xx/xxx/xxxx/xxxxxx.py
echo "End!"
```

2.我是通过apache common-exec包下的CommandLine（java）执行的

```java
commandline = CommandLine.parse("/bin/bash" + " " + "run_script.sh");
```

3.执行后报No such file or directory

4.脚本很简单，路径也核对过是正确的，但是使用cat命令去查看脚本文件时，发现每行结尾多了一些字符(^M$)，如下

```shell
[root@xxxx xxxx]# cat -A /users/jalchu/run_script.sh 
#!/bin/bash^M$
source /opt/xxxx/xxxx.cfg^M$
/opt/venv/bin/python /opt/xxxx/xxxx/xxxx/xxxx.py^M$
echo "End!"^M$
[root@xxxx xxxx]# cd /users/jalchu/
```

5.然后自己从centos重新创建了一个脚本文件，内容自己一行行敲上去，再次使用cat命令查看，发现结尾和上面不一致，如下

```shell
[root@xxxx jalchu]# cat -A /users/jalchu/run_script.sh 
#!/bin/bash$
source /opt/xxxx/xxxx.cfg$
/opt/venv/bin/python /opt/xxxx/xxxx/xxxx/xxxx.py$
echo "End!"$
[root@xxxx jalchu]# cat --help
```

6.此时使用java执行新建的脚本正常

7.原因：之前的脚本是从windows传输过去的，两边对文件格式的定义不太一样，所以报错了，具体没有细看

参考：https://www.cnblogs.com/zijin/p/3501912.html
