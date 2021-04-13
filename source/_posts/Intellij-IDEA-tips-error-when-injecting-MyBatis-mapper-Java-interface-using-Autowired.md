---
title: >-
  Intellij IDEA tips error when injecting MyBatis mapper Java interface using
  @Autowired
date: 2021-04-13 15:50:48
categories: 
- 其它
- 问题解决
tags: error
---

## Background

Intellij IDEA tips inspection error when injecting MyBatis mapper Java interface using @Autowired

```java
@Autowired
private MyMapper mapper;
```

## Error Info

```
Could not autowire. No beans of 'MyMapper' type found.
```

## Solutions

It is not really an error, but error tips are annoying.

Solution 1: Add `@Repository` or `@Component` annotation in top of MyBatis Mapper interface classes.

```java
@Repository
public interface MyMapper {}
```

Solution 2: Using @Resource annotation to inject MyBatis Mapper interface classes.

```java
@Resouce
private MyMapper mapper;
```

## References

[1] [Idea inspects batis mapper bean wrong](https://stackoverflow.com/questions/25379348/idea-inspects-batis-mapper-bean-wrong)