---
title: maven打jar包插件
date: 2018-03-02 11:59:34
tags:
- maven
- jar
categories:
- [学习, Java语言学习, Maven]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1. maven-assembly-plugin第一种方式

```xml
      <!-- maven-assembly-plugin -->
      <!-- 打包方式：mvn package assembly:single  -->
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-assembly-plugin</artifactId>
        <version>2.5.5</version>
        <configuration>
          <archive>
            <manifest>
              <mainClass>com.test.execute.Test</mainClass>
            </manifest>
          </archive>
          <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
          </descriptorRefs>
        </configuration>
      </plugin>
```

```bash
D:\Tools\idea\oa\test\target>java -jar test-jar-with-dependencies.jar
Hello World!
```

2. maven-assembly-plugin第二种方式

```xml
      <!-- maven-assembly-plugin -->
      <!-- 打包方式：mvn package -->
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-assembly-plugin</artifactId>
        <version>2.5.5</version>
        <configuration>
          <archive>
            <manifest>
              <mainClass>com.test.execute.Test</mainClass>
            </manifest>
          </archive>
          <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
          </descriptorRefs>
        </configuration>
        <executions>
          <execution>
            <id>make-assembly</id>
            <phase>package</phase>
            <goals>
              <goal>single</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
```

```bash
D:\Tools\idea\oa\test\target>java -jar test-jar-with-dependencies.jar
Hello World!
```

3. maven-shade-plugin

```xml
      <!-- maven-shade-plugin -->
      <!-- 打包方式：mvn package -->
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>2.4.1</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <transformers>
                <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                  <mainClass>com.test.execute.Test</mainClass>
                </transformer>
              </transformers>
            </configuration>
          </execution>
        </executions>
      </plugin>
```

```bash
D:\Tools\idea\oa\test\target>java -jar test.jar
Hello World!


D:\Tools\idea\oa\test\target>java -jar test-1.0-SNAPSHOT-shaded.jar
Hello World!
```
