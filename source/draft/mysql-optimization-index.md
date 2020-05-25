Title: MySQL 优化（二）：indexing

Content

- Indexing Basics
- Index Strategies
- Index and Table Maintenance
- Conclusion
- References

This post is talking about MySQL index, if you are familiar with this topic, you can go to the conclusion part that's a good summary of the indexing of MySQL that may give you some enlightenment.

Indexes (also called keys in MySQL) are data structures that storage engines use to find rows quickly.

Indexes are critical for good performance, and become more important as your data grows larger. Small, lightly loaded databases often perform well even without proper indexes, but as the dataset grows, performance can drop very quickly.

Index optimization is perhaps the most powerful way to improve query performance. Indexes can improve performance by many orders of magnitude, and optimal indexes can sometimes boost performance about two orders of magnitude more than indexes that merely good. Creating truly optimal indexes will often require you to rewrite queries.

## Indexing Basics

An index contains values from one or more columns in a table. If you index more than one column, the column order is very important, because MySQL can only search efficiently on a leftmost prefix of the index. Creating an index on two columns is not the same as creating two separate single-column indexes.

### Types of Indexes

There are many types of indexes, each designed to perform well for different purposes. Indexes are implemented in the storage engine layer, not the server layer. Thus, they are not standardized: indexing works slightly differently in each engine, and not all engines support all types of indexes. 

**B-Tree Indexes**

When people talk about an index without mentioning a type, they are probably referring to a B-Tree index, which uses a B-Tree data structure to store its data. Most of MySQL's storage engines support this index type.

Storage engines use B-Tree indexes in various ways, which can affect performance. For example, MyISAM uses a prefix compression technique that makes indexes samller, but InnoDB leaves values uncompressed in its indexes. Also, MyISAM indexes refer to the indexed rows by their physical storage locations, but InnoDB refers to them by their primary key values. Each variation has benefits and drawbacks.

A B-Tree index speeds up data access because the storage engine doesn't have to scan the whole table to find the desired data. And you can find a specific row using O(log n) time cost from an index file.

Limitations of B-Tree indexes:

- They are not useful if the lookup does not start from the leftmost side of the indexed columns.
- You can't skip columns in the index. For example, an index of your table is `key(last_name, first_name, birth_date)`, if you skip first_name, MySQL can use only the first column of the index, whether the birth_date appears in the where clause.
- The storage engine can't optimize accesses with any columns to the right of the first range condition. For example, your index is `key(last_name, first_name, birth_date)`, and your query is `WHERE last_name="xx" AND first_name LIKE "xx%" AND birth_date="xxx"`,  the index access will use only the first two columns in the index, because the LIKE is a range condition.

Column order in index is very important, because limitations of B-Tree indexes are all related to column ordering. Some of these limitations are not inherent to B-Tree indexes, but are a result of how the MySQL query optimizer and storage engines use indexes. Some of these limitations might be removed in the future.

**Hash Indexes**

A hash index is built on a hash table and is useful only for exact lookups that use every column in the index. For each row, the storage engine computes a hash code of the indexed columns, which is a small value that will probably differ from the hash codes computerd for other rows with different key values. It stores the hash codes in the index and stores a pointer to each row in a hash table.

Because the indexes themselves store only short hash values, hash indexes are very compact. As a result, lookups are usually ligntning fast. However, hash indexes have some limitations:

- Because the index contains only hash codes and row pointer rather than the values themselves, MySQL can't use the values in the index to avoid reading the rows. Fortunately, accessing the in-memory rows is very fast.
- MySQL can't use hash indexes for sorting because they don't store rows in sorted order.
- Hash indexes don't support partial key matching, because they compute the hash from the entrie indexed value. That is, if you have an index on (A, B) and your query's WHERE clause refers only to A, the index won't help.
- Hash indexes support only equality comparisons that use the =, in(), and <=> operators (note that <> and <=> are not the same operator). They can't speed up range queries.
- Accessing data in hash index is very quick, unless there are many collisions (multiple values with the same hash). When there are collisions, the storage engine must follow each row pointer in the linked list to sequence lookup the right rows.
- Some index maintenance operations (delete or add a row to table) can be slow if there are many hash collisons.

These limitations make hash indexes useful only in special cases. However, when they match the application's needs, they can improve performance dramatically.

If your storage engine doesn't support hash indexes, you can emulate them yourself in a manner similar to that InnoDB uses.

**Full-text Indexes**

FULLTEXT is a special type of index that finds keyworkds in the text instead of  compariing values directly to the values in the index. Full-text searching is completely different from other types of matching. It has many subtleties, such as stopwords, stemming and plurals, and Boolean searching. It is much more analogous to what a search engine does than to simple WHERE parameter matching.

Having a full-text index on a column does not eliminate the value of a B-Tree index on the same column. Full-text indexes are for MATCH AGAINST operations, not ordinary WHERE clause operations.

In standard MySQL, only part of storage engines supports full-text indexing, and there are third-party storage engines for full-text search, such as Sphinx, Lucene, Solr, Groonga, and so on.

### Benefits of Indexes

- Indexes reduce the amount of data the server has to examine.
- Indexes help the server avoid sorting and temporary tables.
- Indexes turn random I/O into sequential I/O.

## Index Strategies

Creating the correct indexes and using them properly is essential to good query performance. There are many ways to choose and use indexes effectively, because there are many special-case optimizations and specialized behaviors. We need to understand how to use indexes effectively.

**Isolating the column**

There are queries that defeat indexes or prevent MySQL from using the available indexes. MySQL generally can't use indexes on columns unless the columns are isolated in the query. **Isolating the column** means it should not be part of an expression or be inside a function in the query. Some examples of don't isolating the column queries.

```sql
'bad query exapmles'
mysql> SELECT actor_id FROM sakila.actor WHERE actor_id + 1 = 5;
mysql> SELECT ... WHERE TO_DAYS(CURRENT_DATE) - TO_DAYS(date_col) <= 10;
```

**Prefix Indexes and Index Selectivity**

Sometimes you need to index very long character columns, which makes you indexes large and slow. One strategy is to simulate a hash index. 

Another way is prefix indexes. You can often save space and get good performance by indexing the first few characters instead of the whole value. This makes your indexes use less space, but it also makes them less selective. Index selectivity is the ratio of the number of distinct indexed values to the total number of rows in the table, and range from 1/rows to 1. A highly selective index is good because it lets MySQL filter out more rows when it looks for matches.

A prefix of the column is often selective enough to give good performance. If you are indexing BLOB or TEXT columns, or very long VARCHAR columns, you must define prefix indexes, because MySQL disallows indexing their full length. To create a prefix index:

```sql
CREATE INDEX index_name
ON table_name(column_name(length));
```

Ways to calculate a good prefix length

- Find the most frequent values and compare that list to a list of the most frequent prefixes.

- Computing the full column's selectivity and trying to make the prefix's selectivity close to that value.

  ```sql
  'full column selectivity'
  mysql> SELECT COUNT(DISTINCT city)/COUNT(*) FROM sakila.city_demo;
  ''
  mysql> SELECT COUNT(DISTINCT LEFT(city, 3))/COUNT(*) AS sel3,
  -> COUNT(DISTINCT LEFT(city, 4))/COUNT(*) AS sel4,
  -> COUNT(DISTINCT LEFT(city, 5))/COUNT(*) AS sel5,
  -> COUNT(DISTINCT LEFT(city, 6))/COUNT(*) AS sel6
  -> FROM sakila.city_demo;
  ```

Prefix indexes can be a great way to make indexes smaller and faster, but they have downsides too: MySQL cannot use prefix indexes for ORDER BY or GROUP BY queries, nor can it use them as covering indexes.

A common case found to benefit from prefix indexes is when long hexadecimal identifiers are used. Adding an index on the first eight characters or so often boosts performance significantly, in a way that's completely transparent to the application.

**Multicolumn Indexes**

Multicolumn indexes are often very poorly understood. Common mistakes are to index many or all of the columns separately, or to index columns in the wrong order.

Individual indexes on lots of columns won't help MySQL improve performance for most queries. Earlier versions of MySQL could only a single index, so when no single index was good enough to help, MySQL often chose a table scan. MySQL 5.0 and newer can cope a little with such poorly indexed tables by using a strategy as index merge, which permits a query to make limited use of multiple indexes from a single table to locate desired rows.

For example, the film_actor table has an index on film_id and an index on actor_id, but neither is a good choice for both WHERE conditions in this query:

```sql
mysql> SELECT film_id, actor_id FROM sakila.film_actor
-> WHERE actor_id = 1 OR film_id = 1;
```

In older MySQL versions, that query would produce a table scan unless you wrote it as UNION of two queries:

```sql
mysql> SELECT film_id, actor_id FROM sakila.film_actor WHERE actor_id = 1
-> UNION ALL
-> SELECT film_id, actor_id FROM sakila.film_actor WHERE film_id = 1
-> AND actor_id <> 1;
```

In MySQL 5.0 and newer, the query can use both indexes, scanning them simultaneously and merging the result.

The index merge strategy sometimes works very well, but it's more common for it to actually be an indication of a poorly indexed table.

When you see an index merge in EXPLAIN, you should examine the query and table structure to see if this is really the best you can get.

**Choosing a Good Column Order**

The correct order depends on the queries that will use the index, and you must think about how to choose the index order such that rows are sorted and gourped in a way that will benefit the query.

The order of columns in a multicolumn B-Tree index means that the index is sorted first by the leftmost column, the by the next column, and so on. Therefore, the index can be scanned in either forward or reverse order, to satisfy queries with ORDER BY, GOURP BY, and DISTINCT clauses that match the column order exactly.





## Index and Table Maintenance

...

## Conclusion

...

## Others

index constract Table

| Index Type   | Indexed Columns Count | Time Complexity | Equality Query                | Range Query                                           | Order and Group | Covering |
| ------------ | --------------------- | --------------- | ----------------------------- | ----------------------------------------------------- | --------------- | -------- |
| B-Tree Index | Multiple              | O(log n)        | Yes. (Leftmost indexed value) | Yes                                                   | Yes             | Yes      |
| Hash Index   | Multiple              | O(1)            | Yes. (Entire indexed value)   | No                                                    | No              | No       |
| Bitmap Index | One                   | O(1)            | Yes                           | No. Indexing on a column that has small range values. | No              | No       |



## References

[1] High Performance MySQL by Baron Schwartz, Vadim Tkachenko, Peter Zaitsev, Derek J. Balling

[2] MySQL 5.7 Reference Manual