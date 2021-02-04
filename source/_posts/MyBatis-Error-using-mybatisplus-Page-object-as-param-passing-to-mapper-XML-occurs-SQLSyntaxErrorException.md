---
title: >-
  MyBatis Error: Using MyBatis-Plus Page object as a parameter passing to mapper XML
  occurs SQLSyntaxErrorException
date: 2021-02-04 17:26:41
categories: 
- 其它
- 问题解决
tags: error
---

## Background

When I using MyBatis-Plus' `com.baomidou.mybatisplus.extension.plugins.pagination.Page` object as one of parameters passing mapper XML to find my page data, there are some errors. 

## Error Info

```
org.springframework.jdbc.BadSqlGrammarException: 
### Error querying database.  Cause: java.sql.SQLSyntaxErrorException: You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'LIMIT 0,10' at line 58
### The error may exist in file [D:\jtg\Repositories\manage-console-app\target\classes\mapper\modules\sysmp\CrmCustomerMapper.xml]
### The error may involve defaultParameterMap
### The error occurred while setting parameters
### SQL: select ... limit 0, 10 LIMIT ?,?
### Cause: java.sql.SQLSyntaxErrorException: You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'LIMIT 0,10' at line 58
; bad SQL grammar []; nested exception is java.sql.SQLSyntaxErrorException: You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'LIMIT 0,10' at line 58
```

## Solutions

Using yourself defined Page object as the parameter, don't use MyBatis-Plus Page object.

## Reasons

When you using MyBatis-Plus Page object as parameter passing to mapper XML, The MyBatis-Plus will automate add the string `LIMIT ?,?` to end of the mapper XML SQL.