---
title: '[JDBC Error] Java.sql.SQLException: Zero Date value Prohibited'
date: 2021-05-26 13:44:38
tags: error
categories:
- 其它
- 问题解决
---

## Background

Using MyBatis to query DATETIME type column data from MySQL table, if column value is `0000-00-00 00:00:00`, program will throw exception `Java.sql.SQLException`. The following is the column properties.

```sql
pubtime DATETIME NULL DEFAULT NULL
```

## Error Info

```
Error attempting to get column 'pubtime' from result set.  Cause: java.sql.SQLException: Zero date value prohibited
; Zero date value prohibited; nested exception is java.sql.SQLException: Zero date value prohibited
org.springframework.dao.TransientDataAccessResourceException: Error attempting to get column 'pubtime' from result set.  Cause: java.sql.SQLException: Zero date value prohibited
; Zero date value prohibited; nested exception is java.sql.SQLException: Zero date value prohibited
```

## Solutions

To set up `zeroDateTimeBehavior=convertToNull` in JdbcUrl. `zeroDateTimeBehavior` values can be `EXCEPTION`, `ROUND` and `CONVERT_TO_NULL`. The default value of `zeroDateTimeBehavior` is `EXCEPTION`.

1. Zero date will be converted to null

```
driver-url=jdbc:mysql://127.0.0.1/test?zeroDateTimeBehavior=convertToNull
```

2. Zero date will be converted to 0001-01-01 00:00:00.0, equivalent to one year

```
driver-url=jdbc:mysql://127.0.0.1/test?zeroDateTimeBehavior=round
```

## Reasons

the MySQL database in the face of 0000-00-00 00:00:00 date processing, if not set corresponding countermeasures, will produce an exception.

## References

- [Java.sql.SQLException:Zero Date value Prohibited exception handling](https://topic.alibabacloud.com/a/javasqlsqlexceptionzero-date-value-prohibited-exception-handling_1_27_30012188.html)
- [Datetime types processing - MySQL Documentation](https://dev.mysql.com/doc/connector-j/8.0/en/connector-j-connp-props-datetime-types-processing.html)
