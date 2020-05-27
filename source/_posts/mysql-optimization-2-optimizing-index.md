---
title: MySQL优化（二）：Indexing
date: 2020-05-27 17:20:16
categories: 
- 性能优化
tags: MySQL
---



This post is talking about MySQL index, if you are familiar with this topic, you can go to the conclusion part that's a good summary of the indexing in MySQL that may give you some enlightenment.

Indexes (also called keys in MySQL) are data structures that storage engines use to find rows quickly.

Indexes are critical for good performance, and become more important as your data grows larger. Small, lightly loaded databases often perform well even without proper indexes, but as the dataset grows, performance can drop very quickly.

Index optimization is perhaps the most powerful way to improve query performance. Indexes can improve performance by many orders of magnitude, and optimal indexes can sometimes boost performance about two orders of magnitude more than indexes that merely good. Creating truly optimal indexes will often require you to rewrite queries.

## Indexing Basics

An index contains values from one or more columns in a table. If you index more than one column, the column order is very important, because MySQL can only search efficiently on a leftmost prefix of the index. Creating an index on two columns is not the same as creating two separate single-column indexes.

### Types of Indexes

There are many types of indexes, each designed to perform well for different purposes. Indexes are implemented in the storage engine layer, not the server layer. Thus, they are not standardized: indexing works slightly differently in each engine, and not all engines support all types of indexes. 

**B-Tree Indexes**

When people talk about an index without mentioning a type, they are probably referring to a B-Tree index, which uses a B-Tree data structure to store its data. Most of MySQL's storage engines support this index type.

Storage engines use B-Tree indexes in various ways, which can affect performance. For example, MyISAM uses a prefix compression technique that makes indexes smaller, but InnoDB leaves values uncompressed in its indexes. Also, MyISAM indexes refer to the indexed rows by their physical storage locations, but InnoDB refers to them by their primary key values. Each variation has benefits and drawbacks.

A B-Tree index speeds up data access because the storage engine doesn't have to scan the whole table to find the desired data. And you can find a specific row using O(log n) time cost from an index file.

Limitations of B-Tree indexes:

- They are not useful if the lookup does not start from the leftmost side of the indexed columns.
- You can't skip columns in the index. For example, an index of your table is `key(last_name, first_name, birth_date)`, if you skip first_name, MySQL can use only the first column of the index, whether the birth_date appears in the where clause.
- The storage engine can't optimize accesses with any columns to the right of the first range condition. For example, your index is `key(last_name, first_name, birth_date)`, and your query is `WHERE last_name="xx" AND first_name LIKE "xx%" AND birth_date="xxx"`,  the index access will use only the first two columns in the index, because the LIKE is a range condition.

Column order in index is very important, because limitations of B-Tree indexes are all related to column ordering. Some of these limitations are not inherent to B-Tree indexes, but are a result of how the MySQL query optimizer and storage engines use indexes. Some of these limitations might be removed in the future.

**Hash Indexes**

A hash index is built on a hash table and is useful only for exact lookups that use every column in the index. For each row, the storage engine computes a hash code of the indexed columns, which is a small value that will probably differ from the hash codes computed for other rows with different key values. It stores the hash codes in the index and stores a pointer to each row in a hash table.

Because the indexes themselves store only short hash values, hash indexes are very compact. As a result, lookups are usually lightning fast. However, hash indexes have some limitations:

- Because the index contains only hash codes and row pointer rather than the values themselves, MySQL can't use the values in the index to avoid reading the rows. Fortunately, accessing the in-memory rows is very fast.
- MySQL can't use hash indexes for sorting because they don't store rows in sorted order.
- Hash indexes don't support partial key matching, because they compute the hash from the entire indexed value. That is, if you have an index on (A, B) and your query's WHERE clause refers only to A, the index won't help.
- Hash indexes support only equality comparisons that use the =, in(), and <=> operators (note that <> and <=> are not the same operator). They can't speed up range queries.
- Accessing data in hash index is very quick, unless there are many collisions (multiple values with the same hash). When there are collisions, the storage engine must follow each row pointer in the linked list to sequence lookup the right rows.
- Some index maintenance operations (delete or add a row to table) can be slow if there are many hash collisions.

These limitations make hash indexes useful only in special cases. However, when they match the application's needs, they can improve performance dramatically.

If your storage engine doesn't support hash indexes, you can emulate them yourself in a manner similar to that InnoDB uses.

**Full-text Indexes**

FULLTEXT is a special type of index that finds keywords in the text instead of  comparing values directly to the values in the index. Full-text searching is completely different from other types of matching. It has many subtleties, such as stopwords, stemming and plurals, and Boolean searching. It is much more analogous to what a search engine does than to simple WHERE parameter matching.

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

The correct order depends on the queries that will use the index, and you must think about how to choose the index order such that rows are sorted and grouped in a way that will benefit the query.

The order of columns in a multicolumn B-Tree index means that the index is sorted first by the leftmost column, the by the next column, and so on. Therefore, the index can be scanned in either forward or reverse order, to satisfy queries with ORDER BY, GOURP BY, and DISTINCT clauses that match the column order exactly.

There is **a rule of thumb** for choosing column order: place the most selective columns first in the index. It can be helpful in some cases, but it's usually much less important than avoiding random I/O and sorting, all things considered. Specific cases vary, so there's no one-size-fits-all rule. This rule of thumb is probably less important than you think.

Placing the most selective columns first can be a good idea when there is no sorting or grouping to consider, and thus the purpose of the index is only to optimize WHERE lookups. For example:

```sql
SELECT * FROM payment WHERE staff_id = 2 AND customer_id = 584;
```

Should you create an index on (staff_id, customer_id), or should you reverse the column order? We can run some quick queries to help examine the distribution of values in the table and determine which column has higher selectivity.

```sql
SELECT SUM(staff_id = 2), SUM(customer_id = 584) FROM payment
```

```
The Result:
SUM(staff_id = 2): 7992
SUM(customer_id = 584): 30
```

According to the rule of thumb, we should place customer_id first in the index, because the predicate matches fewer rows in the table (it has high selectivity).

If you don't have specific samples to run, it might be better to use the rule of thumb, which is to look at the cardinality across the board, not just for one query:

```sql
SELECT COUNT(DISTINCT staff_id)/COUNT(*) AS staff_id_selectivity,
COUNT(DISTINCT customer_id)/COUNT(*) AS customer_id_selectivity,
COUNT(*) 
FROM payment
```

```
THe Result:
staff_id_selectivity: 0.0001
customer_id_selectivity: 0.0373
COUNT(*): 16049
```

customer_id has higher selectivity, so again the answer is to put that column first in the index.

You have to be careful not to assume that average-case performance is representative of special-case performance. Special cases can wreck performance for the whole application.

Although the rule of thumb about selectivity and cardinality is important, other factors--such as sorting, grouping, and the presence of range conditions the query's WHERE clause--can make a much bigger difference to query performance.

**Clustered Indexes**

Clustered indexes aren't separate type of index, but make adjacent key values together. The exact detail vary between implementations, but InnoDB's clustered indexes actually store a B-Tree index. The term "clustered" refers to the fact that rows with adjacent key values are stored close to each other. 

You can have only one clustered index per table, because you can't store the rows in two places at once. Storage engines are responsible for implementing indexes, but not all storage engines support clustered indexes.

InnoDB clusters the data by the primary key. If you don't define a primary key, InnoDB will try to use a unique nonnullable index instead. If there is no such index, InnoDB will define a hidden primary key for you and then cluster on that. InnoDB clusters records together only within a page. 

A clustering primary key can help performance, but it can also cause serious performance problems.  Thus, you should think carefully about clustering, especially when you change a table's storage engine from InnoDB to something else.

Advantages of clustering indexes

- It keeps related data close together.
- Data access is fast.

Disadvantages of clustered indexes

- Insert speeds depend heavily on insertion order. Inserting rows in primary key order is the fastest way.
- Updating the clustered index columns is expensive, because it forces InnoDB to move each updated row to a new location.
- Secondary (nonclustered) indexes access require two index lookups instead of one. Because it stores the row's primary key values rather than stores the pointer to the row's physical location.

**Covering Indexes**

An index that contains (or covers) all the data needed to satisfy a query is called a covering index. In other words, a covering index is a multi-columns index that indexes on all columns needed for your queries. In MySQL, secondary indexes in your table default contain the primary key column values.

Benefits of covering indexes

- Index entries are usually much smaller than the full row size, so MySQL can access significantly less data if it reads only the index.
- Indexes are sorted by their index values, so I/O-bound range accesses will need to do less I/O compared to fetching each row from a random disk location.

A covering index can't be any kind of index. The index must store the values from the columns it contains. Hash, spatial, and full-text indexes don't store these values, so MySQL can use only B-Tree indexes to cover queries.

When you issue a query that is covered by an index, you will see "Using index" in the Extra column in EXPLAIN. For example, the inventory table has a multicolumn index on (store_id, film_id)

```sql
EXPLAIN SELECT store_iid, file_id FROM inventory;
```

```
Result:
...
Extra: Using index
```

**Using Index Scans for Sorts**

MySQL has two ways to produce ordered result: using a sort operation, or scanning an index in order. You can tell when MySQL plans to scan an index by looking for "index" in the `type` column in EXPLAIN.

Scanning the ordered index itself is fast, because it simply requires moving from one index entry to the next. However, if MySQL isn't using the index to scanning, it will have to look up each row it finds in the index. Reading data in index order is usually much slower than a sequential table scan, especially for I/O-bound workloads. 

MySQL can use the same index for both sorting and finding rows. It's a good idea to design your indexes so that they're useful for both tasks at once.

Ordering the result by the index works:

- only when the index's order is exactly the same as the ORDER BY clause and all columns are sorted in the same direction (ascending or descending). 
- If the query joins multiple tables, it works only when all columns in the ORDER BY clause refer to the first table. 
- The ORDER BY clause also needs to from a leftmost prefix of the index.
- One case where the ORDER BY clause doesn't have to specify a leftmost prefix of the index is if there are constants for the leading columns. If the WHERE clause or a JOIN clause specifies constants for these columns, they can "fill the gaps" in the index.

**Packed (Prefix-Compressed) Indexes**

Prefix compression reduce index size, allowing more of the index to fit in memory and dramatically improving performance in some cases. 

Compressed blocks use less space, but they make some operations slower. For example, binary searches, ORDER BY. 

The trade-off is one of CUP and memory resources versus disk resources. Packed indexes can be about one-tenth the size on disk, and if you have an I/O-bound workload they can more than offset the cost for some queries.

You can control how a table's indexes are packed with the PACK_KEYS option to CREATE TABLE or ALTER TABLE. For example,

```sql
CREATE  TABLE `test_database`.`new_table` (
  `id` INT NOT NULL ,
  `address` VARCHAR(45) NULL ,
  PRIMARY KEY (`id`) ,
  INDEX `address` (`address` ASC) )
  PACK_KEYS = 1;
```

This option PACK_KEYS can have following three values: 1, 0, DEFAULT. Set this option to 1 to pack indexes of all kind of columns. Set it to 0 will disable all kind of indexes compression. Set it to DEFAULT will pack only CHAR, VARCHAR, BINARY, or VARBINARY type columns. 

Conclusion of using packed indexes

- Packed indexes may give great space savings.
- PACK_KEYS applies to MyISAM tables only.
- Packed indexes can slow down your integer joins a lot.
- Packed indexes will make you read faster and updates will become slower.

**Redundant and Duplicate Indexes**

MySQL allows you to create multiple indexes on the same column; it does not notice and protect you from your mistake. MySQL has to maintain each duplicate index separately, and the query optimizer will consider each of them when it optimizes queries.

**Duplicate indexes** are indexes of the same type, created on the same set of columns in the same order. You should try to avoid creating them, and you should remove them if you find them.

Sometimes you can create duplicate indexes without knowing it. For example:

```sql
CREATE TABLE table_name(
    ID INT NOT NULL PRIMARY KEY,
    ...
    UINQUE(ID),
    INDEX(ID)
)
```

MySQL implements UNIQUE constraints and PRIMARY KEY constraints with indexes, so this example actually creates three indexes on the same column.

**Redundant indexes** are a bit different from duplicate indexes. If there is an index on (A, B), another index on (A) would be redundant because it is a prefix of the first index. The index on (A, B) can also be used as an index on (A) when they are B-Tree indexes. 

Redundant indexes usually appear when people add indexes to a table. In most cases you don't want redundant indexes, and to avoid them you should extend existing indexes rather than add new ones. Still, there are times when you will need redundant indexes for performance reasons. Extending an existing index might make it much larger and reduce performance for some queries.

The drawback of having two indexes is the maintenance cost. Inserting new rows into the table with more indexes is slower. Adding new indexes might have a performance impact for INSERT, UPDATE, DELETE operations, especially if a new index causes you to hit memory limits.

The solution for redundant and duplicate indexes is simply to drop them, but first you need to identify them. You can write various complicated queries against the INFORMATION_SCHEMA tables. You can also use tools to analyze them, such as common_schema, and pt-duplicate-keychecker tool.

Be careful when determining which indexes are candidates for dropping or extending. It's good to validate your planned changes carefully with a tool such as pt-upgrade from Percona Toolkit.

**Unused Indexes**

In addition to duplicate and redundant indexes, you might have some indexes that the server simply doesn't use. You should consider dropping them. There are two tools that can help you identify unused indexes. The easiest and most accurate is the INFORMATION_SCHEMA.INDEX_STATISTICS table in Percona Server and MariaDB. Alternatively, you can use the pt-index-usage tool included in Percona Toolkit.

**Indexes and Locking**

Indexes permit queries to lock fewer rows. If you queries never touch rows they don't need, they'll lock fewer rows, and that's better for performance.

InnoDB locks rows only when it accesses them, and an index can reduce the number of rows InnoDB accesses and therefore locks. However, this works only if InnoDB can filter out the undesired rows at the storage engine level. For example, 

```sql
SET AUTOCOMMIT=0;
BEGIN;
SELECT actor_id FROM actor WHERE actor_id < 5 
    AND actor_id <> 1 FOR UPDATE;
```

```
Result:
+----------+
| actor_id |
+----------+
|        2 |
|        3 |
|        4 |
+----------+
```

This query returns only rows 2 through 4, but it actually gets exclusive locks on rows 1 through 4. InnoDB locked row 1 because the plan MySQL chose for this query was an index range access:

```sql
EXPLAIN SELECT actor_id FROM actor WHERE actor_id < 5 
    AND actor_id <> 1 FOR UPDATE;
```

```
Result:
+----+-------------+-------+-------+---------+--------------------------+
| id | select_type | table | type  | key     | Extra                    |
+----+-------------+-------+-------+---------+--------------------------+
| 1  | SIMPLE      | actor | range | PRIMARY | Using where; Using index |
+----+-------------+-------+-------+---------+--------------------------+
```

In other words, the low-level storage engine operation was "begin at the start of the index and fetch all rows until actor_id < 5 is false". The server didn't tell InnoDB about the WHERE condition that eliminated row 1. Note the presence of "Using where" in the Extra column in EXPLAIN. This indicates that MySQL server is applying a WHERE filter after the storage engine returns the rows.

## Index and Table Maintenance

Once you have created tables with proper data types and added indexes, you work isn't over: you still need to maintain your tables and indexes to make sure they perform well. The three main goals of table maintenance are: finding and fixing corruption, maintaining accurate index statistics, and reducing fragmentation.

**Finding and Repairing Table Corruption**

The worst thing is table corruption. This often happens due to crashes. However, all storage engines can experience index corruption due to hardware problems or internal bugs in MySQL or the operating system.

Corrupted indexes can cause queries to return incorrect results, raise duplicate-key errors when there is no duplicate value, or even cause lockups and crashes. If you experience odd behavior you can run CHECK TABLE to see if the table is corrupt. CHECK TABLE usually catches most table and index errors. (Note that some storage engines don't support this command.)

You can fix corrupt tables with the REPAIR TABLE command, but not all storage engines support this.

Alternatively, you can either use an offline engine-specific repair utility, such as myisamchk, or dump the data and reload it.

If you experience data corruption, the most important thing to do is try to determine why it's occurring; don't simply repair the data, or the corruption could return.

**Updating Index Statistics**

The MySQL query optimizer uses two API calls to ask the storage engines how index values are distributed when deciding how to use indexes. The first is the records_in_range() call, which accepts range end points and returns the number of records in that range. 

The second API call is info(), which can return various types of data, including index cardinality (approximately how many records there are for each key value).

If the statistics were never generated, or if they are out of date, the optimizer can make bad decisions. The solution is to run ANALYZE TABLE, which regenerates the statistics.

Each storage engine implements index statistics differently, so the frequency with which you'll need to run ANALYZE TABLE differs, as does the cost of running the statement:

- The memory storage engine does not store index statistics at all.
- MyISAM stores statistics on disk, and ANALYZE TABLE performs a full index scan to compute cardinality. The entire table is locked during this process.
- InnoDB does not store statistics on disk as of MySQL 5.5, but rather estimates them with random index dives and stores them in memory.

You can examine the cardinality of your indexes with the SHOW INDEX FROM command. 

InnoDB calculates statistics for indexes when tables are first opened, when you run ANALYZE TABLE, and when the table's size changes significantly.

For even more query plan stability, and for faster system warmups, you can use a system table to store index statistics so they are stable across server restarts and don't need to be recomputed when InnoDB opens the table for the first time after booting up. Index statistics persistence in MySQL 5.6 controlled by the innodb_analyze_is_persistent option.

If you configure your server not to update index statistics automatically, you need to do it manually with periodic ANALYZE TABLE commands, unless you know that the statistics won't change in ways.

**Reducing Index and Data Fragmentation**

B-Tree indexes can become fragmented, which might reduce performance. Fragmented indexes can be poorly filled and non-sequential on disk.

The table's data storage can also become fragmented. However, data storage fragmentation is more complex than index fragmentation. There are three types of data fragmentation:

- Row fragmentation
- Intra-row fragmentation
- Free space fragmentation

MyISAM tables might suffer from all types of fragmentation, but InnoDB never fragments short rows; it moves them and rewrites them in a single piece.

To defragment data, you can either run OPTIMIZE TABLE or dump and reload data. These approaches work for most storage engines. For storage engines that don't support OPTIMIZE TABLE, you can rebuild the table with a no-op ALTER TABLE. Just alter the table to have the same engine it currently uses:

```sql
ALTER TABLE <table> ENGINE=<engine>;
```

Don't assume that you need to defragment your index and tables--measure them first to find out. Percona XtraBackup has a --stats option that can help for to measure fragmentation .

## Conclusion

Indexing is a very complex topic. There are no fixed rules to guide how to indexing. It depends on many factors, such as what are your data structures, how did you use the data in your application, what results are you want, how did storage engines implement the index, properties of each type of index. If you want to improve a slow query, you can profiling the query and to see which subtask costs the most time. **Note that Indexes are good for queries, but too many indexes may slow to write operations.**

There are some basic principles to choose indexes:

- General using of index is the B-Tree index, and the other types of indexes are rather more suitable for special purposes.
- Reads data from index files are faster than reads from physical data files.
- Single-row access is slow. You better to read in a block that contains lots of rows you need.
- Accessing ranges of rows in order is fast. First, sequential I/O doesn't require disk seek, so it is faster than random I/O. Secondly, it doesn't need to perform any follow-up work to sort it.
- Index-only access is fast. If an index contains all the columns that the query needs, the storage engine don't need to find the other columns by looking up rows in the table. This avoids lots of single-row access.

General process for choosing indexes:

1. Choosing a type of index
2. Determining what properties of the index to use.
   - prefix indexes and compressed indexes
   - multicolumn indexes (like B-Tree index, hash index) and covering indexes (like B-Tree index), also consider indexes columns order)
3. Avoid to add redundant and duplicate indexes, and remove unused indexes.

It's very important to be able to reason through how indexes work, and to choose them based on that understanding, not on rules of thumb or heuristics such as "place the most selective columns first in multicolumn indexes" or "you should index all of the columns that appear in the WHERE clause".

If you find a query that doesn't benefit from all of these possible advantages of indexes, see if a better index can be created to improve performance. If not, perhaps a rewrite can transform the query so that it can use an index.

## Appendixes

I. Indexes contrast Table

| Index Type   | Indexed Columns | Time Complexity | Equality Query                | Range Query                                           | Order and Group Query | Covering Index |
| ------------ | --------------- | --------------- | ----------------------------- | ----------------------------------------------------- | --------------------- | -------------- |
| B-Tree Index | Multiple        | O(log n)        | Yes. (Leftmost indexed value) | Yes                                                   | Yes                   | Yes            |
| Hash Index   | Multiple        | O(1)            | Yes. (Entire indexed value)   | No                                                    | No                    | No             |
| Bitmap Index | One             | O(1)            | Yes                           | No. Indexing on a column that has small range values. | No                    | No             |



## References

[1] High Performance MySQL by Baron Schwartz, Vadim Tkachenko, Peter Zaitsev, Derek J. Balling

[2] [Speed up your queries using the covering index in MySQL](https://blog.toadworld.com/2017/04/06/speed-up-your-queries-using-the-covering-index-in-mysql)