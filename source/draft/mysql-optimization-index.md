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

A B-Tree index speeds up data access because the storage engine doesn't have to scan the whole table to find the desired data.

**Hash Indexes**

**Full-text Indexes**

**Other types of Index**

### Benefits of Indexes



## References