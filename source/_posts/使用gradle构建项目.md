---
title: 使用gradle构建项目
date: 2017-09-06 14:15:33
tags:
- gradle
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

> spring官网例子

# 创建项目目录结构
src的根目录是demo，即demo/src...
```
└── src
    └── main
        └── java
            └── hello
```

# 创建文件
① src/main/java/hello/HelloWorld.java
```java
package hello;
 
public class HelloWorld {
  public static void main(String[] args) {
    Greeter greeter = new Greeter();
    System.out.println(greeter.sayHello());
  }
}
```
②  src/main/java/hello/Greeter.java
```java
package hello;
 
public class Greeter {
  public String sayHello() {
    return "Hello world!";
  }
}
```

# 添加gradle
下载gradle二进制包：http://www.gradle.org/downloads，把bin目录添加到path变量中

① 测试添加是否成功，命令：可以使用gradle或gradle -v来检测
```
gradle
```
```
C:\Users\admin>gradle
Starting a Gradle Daemon (subsequent builds will be faster)

> Task :help

Welcome to Gradle 4.1.

To run a build, run gradle <task> ...

To see a list of available tasks, run gradle tasks

To see a list of command-line options, run gradle --help

To see more detail about a task, run gradle help --task <task>

BUILD SUCCESSFUL in 6s
1 actionable task: 1 executed
```
```
gradle -v
```
```
C:\Users\admin>gradle -v


------------------------------------------------------------
Gradle 4.1
------------------------------------------------------------

Build time:   2017-08-07 14:38:48 UTC
Revision:     941559e020f6c357ebb08d5c67acdb858a3defc2

Groovy:       2.4.11
Ant:          Apache Ant(TM) version 1.9.6 compiled on June 29 2015
JVM:          1.8.0_112 (Oracle Corporation 25.112-b15)
OS:           Windows 10 10.0 amd64
```

② 查看gradle可以做些什么
```
gradle tasks
```
```
C:\Users\admin>gradle tasks

> Task :tasks

------------------------------------------------------------
All tasks runnable from root project
------------------------------------------------------------

Build Setup tasks
-----------------
init - Initializes a new Gradle build.
wrapper - Generates Gradle wrapper files.

Help tasks
----------
buildEnvironment - Displays all buildscript dependencies declared in root project 'admin'.
components - Displays the components produced by root project 'admin'. [incubating]
dependencies - Displays all dependencies declared in root project 'admin'.
dependencyInsight - Displays the insight into a specific dependency in root project 'admin'.
dependentComponents - Displays the dependent components of components in root project 'admin'. [incubating]
help - Displays a help message.
model - Displays the configuration model of root project 'admin'. [incubating]
projects - Displays the sub-projects of root project 'admin'.
properties - Displays the properties of root project 'admin'.
tasks - Displays the tasks runnable from root project 'admin'.

To see all tasks and more detail, run gradle tasks --all

To see more detail about a task, run gradle help --task <task>


BUILD SUCCESSFUL in 0s
1 actionable task: 1 executed
```

# 添加配置文件
在项目根目录demo下创建build.gradle文件，文件添加一行
```
apply plugin: 'java'
```
此时可以使用gradle tasks查看一下，发现会多出一部分任务tasks
```
D:\Tools\idea\workspace\spring-boot\demo>gradle tasks
 
> Task :tasks
 
------------------------------------------------------------
All tasks runnable from root project
------------------------------------------------------------
 
Build tasks
-----------
assemble - Assembles the outputs of this project.
build - Assembles and tests this project.
buildDependents - Assembles and tests this project and all projects that depend on it.
buildNeeded - Assembles and tests this project and all projects it depends on.
classes - Assembles main classes.
clean - Deletes the build directory.
jar - Assembles a jar archive containing the main classes.
testClasses - Assembles test classes.
 
Build Setup tasks
-----------------
init - Initializes a new Gradle build.
wrapper - Generates Gradle wrapper files.
 
Documentation tasks
-------------------
javadoc - Generates Javadoc API documentation for the main source code.
 
Help tasks
----------
buildEnvironment - Displays all buildscript dependencies declared in root project 'demo'.
components - Displays the components produced by root project 'demo'. [incubating]
dependencies - Displays all dependencies declared in root project 'demo'.
dependencyInsight - Displays the insight into a specific dependency in root project 'demo'.
dependentComponents - Displays the dependent components of components in root project 'demo'. [incubating]
help - Displays a help message.
model - Displays the configuration model of root project 'demo'. [incubating]
projects - Displays the sub-projects of root project 'demo'.
properties - Displays the properties of root project 'demo'.
tasks - Displays the tasks runnable from root project 'demo'.
 
Verification tasks
------------------
check - Runs all checks.
test - Runs the unit tests.
 
Rules
-----
Pattern: clean<TaskName>: Cleans the output files of a task.
Pattern: build<ConfigurationName>: Assembles the artifacts of a configuration.
Pattern: upload<ConfigurationName>: Assembles and uploads the artifacts belonging to a configuration.
 
To see all tasks and more detail, run gradle tasks --all
 
To see more detail about a task, run gradle help --task <task>
 
 
 
BUILD SUCCESSFUL in 1s
1 actionable task: 1 executed
```
```
gradle build
```

# 依赖管理
gradle依赖管理，加入我们的HelloWorld.java代码中引入了joda time包
```
package hello;
 
import org.joda.time.LocalTime;
 
public class HelloWorld {
  public static void main(String[] args) {
    LocalTime currentTime = new LocalTime();
    System.out.println("The current local time is: " + currentTime);
 
    Greeter greeter = new Greeter();
    System.out.println(greeter.sayHello());
  }
}
```
此时修改build.gradle.
```
repositories {
    mavenCentral()
}
```
```

sourceCompatibility = 1.8
targetCompatibility = 1.8
 
dependencies {
    compile "joda-time:joda-time:2.2"
    testCompile "junit:junit:4.12"
}
```
```
jar {
    baseName = 'gs-gradle'
    version =  '0.1.0'
}
```
```
gradle build
```

# 使用gradle Wrapper
```
$ gradle wrapper --gradle-version 2.13
```
```
└── <project folder>
    └── gradlew
    └── gradlew.bat
    └── gradle
        └── wrapper
            └── gradle-wrapper.jar
            └── gradle-wrapper.properties
```
```
./gradlew build
```
```
build
├── classes
│   └── main
│       └── hello
│           ├── Greeter.class
│           └── HelloWorld.class
├── dependency-cache
├── libs
│   └── gs-gradle-0.1.0.jar
└── tmp
    └── jar
        └── MANIFEST.MF
```

# 我的gradle Wrapper执行没有下载下来，后续操作没做
```
apply plugin: 'application'

mainClassName = 'hello.HelloWorld'
```
```
$ ./gradlew run
:compileJava UP-TO-DATE
:processResources UP-TO-DATE
:classes UP-TO-DATE
:run
The current local time is: 16:16:20.544
Hello world!

BUILD SUCCESSFUL

Total time: 3.798 secs
```
