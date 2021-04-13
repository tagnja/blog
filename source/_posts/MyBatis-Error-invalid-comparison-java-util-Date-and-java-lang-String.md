---
title: 'MyBatis Error: invalid comparison java.util.Date and java.lang.String'
date: 2021-04-13 15:12:05
categories: 
- 其它
- 问题解决
tags: error
---

## Background

Compare date in where clauses of MyBatis mapper XML query.

```xml
<if test="beginTime != null and beginTime != ''">
    createTime >= #{beginTime} and
</if>
```

## Error Info

```
nested exception is org.apache.ibatis.exceptions.PersistenceException: 
### Error querying database.  Cause: java.lang.IllegalArgumentException: invalid comparison: java.util.Date and java.lang.String
### Cause: java.lang.IllegalArgumentException: invalid comparison: java.util.Date and java.lang.String
org.mybatis.spring.MyBatisSystemException: nested exception is org.apache.ibatis.exceptions.PersistenceException: 
### Error querying database.  Cause: java.lang.IllegalArgumentException: invalid comparison: java.util.Date and java.lang.String
### Cause: java.lang.IllegalArgumentException: invalid comparison: java.util.Date and java.lang.String
```

## Solutions

remove `beginTime != ''` in the `<if>` element of MyBatis mapper XML file.

```xml
<if test="beginTime != null">
    createTime >= #{beginTime} and
</if>
```

## Reasons

When you pass `Date` objects as a parameter to MyBatis mapper XML query, you can't compare the `Date` object with a `String` object in MyBatis `<if>` elements.