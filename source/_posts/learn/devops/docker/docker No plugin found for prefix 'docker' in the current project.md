---
title: docker No plugin found for prefix 'docker' in the current project
date: 2020-01-13 15:52:31
tags:
- docker
categories:
- [学习, DevOPS, Docker]
---

## 原因

springboot项目pom里引入了docker-maven-plugin，使用mvn clean package docker:build时遇到该问题

```xml
<plugin>
	<groupId>com.spotify</groupId>
	<artifactId>docker-maven-plugin</artifactId>
	<version>1.2.0</version>
	<configuration>
		<imageName>${project.artifactId}:${project.version}</imageName>
		<dockerDirectory>${project.basedir}</dockerDirectory>
		<resources>
			<resource>
				<targetPath>/</targetPath>
				<directory>${project.build.directory}</directory>
				<include>${project.build.finalName}.jar</include>
			</resource>
		</resources>
	</configuration>
</plugin>
```

## 修改方法

修改maven下的配置文件settings.xml，重新build一下

```xml
<settings>
    <pluginGroups>
        <pluginGroup>com.spotify</pluginGroup>
    </pluginGroups>
</settings>
```

参考：https://github.com/spotify/docker-maven-plugin/issues/322#issuecomment-316604031

