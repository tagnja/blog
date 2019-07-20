---
title: SpringFoxError- Unable to infer base url
date: 2019-07-20 17:12:34
categories: 
- 其它
- 问题解决
tags: error
---

### Operation

I want to use Swagger with Spring Boot, but the Swagger-UI can't work. Following it's my code.

pom.xml

```xml
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
    <dependency>
        <groupId>io.springfox</groupId>
        <artifactId>springfox-swagger2</artifactId>
        <version>2.9.2</version>
    </dependency>
    <dependency>
        <groupId>io.springfox</groupId>
        <artifactId>springfox-swagger-ui</artifactId>
        <version>2.9.2</version>
    </dependency>
</dependencies>
```

AppConfig.java

```java
@Configuration
@EnableSwagger2
public class SpringFoxConfig
{
    @Bean
    public Docket api()
    {
        return new Docket(DocumentationType.SWAGGER_2)
                .select()
                .apis(RequestHandlerSelectors.any())
                .paths(PathSelectors.any())
                .build();
    }
}
```



### Error Info

When I visited http://localhost:8080/swagger-ui.html, occur following error tips.

![](https://bog-1259597974.cos.ap-chengdu.myqcloud.com/190720-SpringFox-Unable-to-infer-base-url.png)

### Solution

make sure your swagger configuration is under your spring boot application which can be scanned.
and then it solved.

### Reason

My config file doesn't scan by spring boot. It leads to the required bean is not exist.

### References

https://github.com/springfox/springfox/issues/1996