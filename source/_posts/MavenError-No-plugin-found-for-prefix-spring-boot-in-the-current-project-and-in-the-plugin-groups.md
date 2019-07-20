---
title: >-
  MavenError- No plugin found for prefix 'spring-boot' in the current project and in
  the plugin groups
date: 2019-07-20 13:39:17
categories: 
- 其它
- 异常解决
tags: exception
---





### Operation

I use `mvn spring-boot:run` command to run my spring boot project.

### Error Info

```
[ERROR] No plugin found for prefix 'spring-boot' in the current project and in the plugin groups [org.apache.maven.plugins, org.codehaus.mojo] available from the repositories [local (D:\Repositories\mavenRepo), central (https://
repo.maven.apache.org/maven2)] -> [Help 1]
[ERROR]
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR]
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/NoPluginFoundForPrefixException
```

### Solution

Make sure pom.xml exist in the directory, when using the mvn spring-boot:run command. No need to add any thing in the pom.xml file.

`pom.xml`

```
<parent>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-parent</artifactId>
	<version>2.1.6.RELEASE</version>
</parent>

<dependencies>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-web</artifactId>
	</dependency>
</dependencies>

<build>
	<plugins>
		<!-- run $ mvn package, generating an executable jar -->
		<plugin>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-maven-plugin</artifactId>
		</plugin>
	</plugins>
</build>
```

### Reason

I run `mvn spring-boot:run` command not in my project root directory

### Reference

https://stackoverflow.com/questions/30855864/maven-no-plugin-found-for-prefix-spring-boot-in-the-current-project-and-in-th