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

FULLTEXT is a special type of index that finds keyworkds in the text instead of  compariing values directly to the values in the index. Full-text searching is completely different from other types of matching.

...



**Other types of Index**

...

### Benefits of Indexes

...

## Index Strategies

...

## Index and Table Maintenance

...

## Conclusion

...

## Others

index constract Table

| Index Type   | Indexed Columns Count | Equality Query                | Range Query | Order Query | Covering |
| ------------ | --------------------- | ----------------------------- | ----------- | ----------- | -------- |
| B-Tree Index | Multiple              | Yes. (Leftmost indexed value) | Yes         | Yes         | Yes      |
| Hash Index   | Multiple              | Yes. (Entire indexed value)   | No          | No          | No       |
| Bitmap Index |                       |                               |             |             |          |



## References

[1] High Performance MySQL by Baron Schwartz, Vadim Tkachenko, Peter Zaitsev, Derek J. Balling

[2] MySQL 5.7 Reference Manual