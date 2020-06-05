---
title: MySQL优化总结
date: 2020-06-05 14:36:31
categories: 
- 性能优化
tags: MySQL
---



## Principles of MySQL Optimization

Using general principles and best practices to create your table schema.

Understanding the structure and properties of different kinds of indexes, and to choose a proper kind of index.

According to query statements in your application to create indexes, creates as few indexes as possible. And makes your indexes covering as more frequently selected columns as possible.

Using EXPLAIN to check how a query execution.

Using query profiling to determine which strategy is optimal, or which query is faster.

## Common Strategies for MySQL Optimization

- Schema
  - Choosing optimal data types.
  - Denormalization.
  - Cache and Summary Tables.
- Index 
  - Common indexes such as B-Tree, Hash, and Bitmap.
  - Multi-column indexes and covering indexes, prefix indexes and compressed indexes.
  - Avoid to add redundant and duplicate indexes, and remove unused indexes.
- Query
  - Queries tricks. For example, chopping up a query (or finding difference set).
  - Join decomposition. Converting join to single-table queries.
  - Using query optimizer hints.
  - Optimizing specific types of queries.

