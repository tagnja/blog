---
title: >-
  MySQL Error - ERROR 1205 (HY000): Lock wait timeout exceeded; try restarting
  transaction
date: 2020-10-28 17:04:38
categories: 
- 其它
- 问题解决
tags: error
toc: false
---

## Errors

```
ERROR 1205 (HY000): Lock wait timeout exceeded; try restarting transaction
```

## Solutions

### Check InnoDB status for locks

```sql
SHOW ENGINE InnoDB STATUS;
```

### Check MySQL open tables

```sql
SHOW OPEN TABLES WHERE In_use > 0;
```

### Check pending InnoDB transactions

```sql
SELECT * FROM `information_schema`.`innodb_trx` ORDER BY `trx_started`; 
```

### Check lock dependency - what blocks what

```sql
SELECT * FROM `information_schema`.`innodb_locks`;
```

### Show running threads get Thread ID

```sql
SHOW PROCESSLIST;
```

### Kill Threads By Thread ID

```
KILL {thread_id};
```



## References

[1] [Fixing “Lock wait timeout exceeded; try restarting transaction” for a 'stuck" Mysql table?](https://stackoverflow.com/questions/2766785/fixing-lock-wait-timeout-exceeded-try-restarting-transaction-for-a-stuck-my)