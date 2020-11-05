---
title: Create Maven Projects with Java 11
date: 2020-11-05 15:07:34
tags: java
categories:
- Java
- Java 基础
---

## Configuring OS Environments

### Configuring Environments on Windows

1. Add environment variable `JAVA_HOME={jdk11_path}`.
2. Add `%JAVA_HOME%\bin` to environment variable `path`.
3. Verify Java version. `$ java -version`.
4. Restart IDE.

**Note**: After updating Java version of environment variable, you need restart your IDE.

## Configuring Maven Project `pom.xml` 

> By default your version of Maven might use an old version of the `maven-compiler-plugin` that is not compatible with Java 9 or later versions. To target Java 9 or later, you should at least use version 3.6.0 of the `maven-compiler-plugin` and set the `maven.compiler.release` property to the Java release you are targetting (e.g. 9, 10, 11, 12, etc.). [1]



```xml
    <properties>
        <maven.compiler.release>11</maven.compiler.release>
    </properties>

    <build>
        <pluginManagement>
            <plugins>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.8.1</version>
                </plugin>
            </plugins>
        </pluginManagement>
    </build>
```

or

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.8.1</version>
    <configuration>
        <release>11</release>
    </configuration>
</plugin>
```



## Configuring IDE

### Configuring Java 11 for Intellij IDEA

1. Open Project Settings (Ctrl + Shift + Alt + S) --> Project --> Project SDK: 11, Project language level: 11. 
2. Open Settings (Ctrl + Alt + S) --> Build, Execution, Deployment --> Compiler --> Java Compiler --> Project bytecode version: 11

## Verification

Running test

```
$ mvn test
```

## References

[1] [Maven in 5 Minutes](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html)