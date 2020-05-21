---
title: MySQL优化（一）：Optimizing Schema
date: 2020-05-21 12:09:29
categories: 
- 性能优化
tags: MySQL
---



This post is talking about MySQL schema optimization, if you are familiar with this topic, you can go to the conclusion part that's a good summary of the schema optimization of MySQL that may give you some enlightenment.

Good logical and physical design is the cornerstone of high performance, and you must design your schema for the specific queries you will run. This often involves trade-offs. For example, a denormalized schema can speed up some types of queries but slow down others. Adding counter and summary tables is a great way to optimize queries, but they can be expensive to maintain.

## Choosing Optimal Data Types

General principles for choosing better data types

- **Smaller is usually better.** Try to use the smallest data type that can correctly store and represent your data. Smaller data types are usually faster, because they use less space on disk, in memory, and in the CPU cache. They also generally require fewer CPU cycles to process.
- **Simple is good.** Fewer CPU cycles are typically required to process operations on simpler data types. For example, integers are cheaper to compare than characters. because character sets and collations (sorting rules) make character comparisons complicated.
- **Avoid NULL if possible.** It's usually best to specify columns as NOT NULL unless you intend to store NULL in them. It's harder for MySQL to optimize queries that refer to nullable columns, because they make indexes, index statistics, and value comparisons more complicated.

Steps to choosing better data types

1. Determining what general class of types is appropriate: numeric, string, temporal, and so on.
2. Choosing the specific type. Many of MySQL's data types can store the same kind of data but vary in the range of values they can store, the precision they permit, or the physical space (on disk and in memory) they require. Some data types also have special behaviors or properties. For example, DATETIME and TIMESTAMP can store the same kind of data: date and time, to a precision of one second. However, TIMESTAMP use only half as much storage space, and have time zone autoupdating capabilities.

MySQL supports many **aliases** for compatibility, such as INTEGER, BOOL, and NUMERIC. These are only aliases, don't affect performance. You can use SHOW CREATE TABLE to see base types rather than aliases you used.

### Numbers

There are two kinds of numbers: whole numbers and real numbers (numbers with a fractional part). 

**Whole Numbers**

**Integer types**: TINYINT, SMALLINT, MEDIUMINT, INT, and BIGINT. These require 8, 16, 24, 32 and 64 bits of storage space, respectively. They can store values from -2^(N-1) to 2^(n-1)-1, where N is the number of bits of storage space they use.

Integer types can optionally have the **UNSIGNED attribute**, which disallows negative values and approximately doubles the upper limit of positive values. For example, a TINYINT UNSIGNED can store values ranging from 0 to 255 instead of from -128 to 127.

Your choice determines how MySQL stores the data, in memory and on disk. However, integer computations generally use 64-bit BIGINT integers, even on 32-bit architectures. (The exceptions are some aggregate functions, which use DECIMAL or DOUBLE to perform computations.)

MySQL lets you **specify a width** for integer types, such as INT(11). This meaningless for most applications: it does not restrict the legal range of values, but simply specifies the number of characters MySQL's interactive tools (such as the command-line client).

**Real Numbers**

Real numbers are numbers that have a fractional part. However, they aren't just for fractional numbers. You can also use DECIMAL to store integers that are so large they don't fit in BIGINT.

The FLOAT and DOUBLE types support **approximate calculations** with standard floating point math. The DECIMAL type is for storing exact fractional numbers. it supports **exact math**.

Floating-point math is significantly faster than decimal math, because the CPU performs the computations natively.

Both floating-point and DECIMAL types let you **specify a precision**. For a DECIMAL column, you can specify the maximum allowed digits and the decimal point. For example, DECIMAL(5, 2) be able to store any value with five digits and two decimals, so it can store values from -999.99 to 999.99.

MySQL use DOUBLE for its internal calculations on floating-point types.

Note: Because of the additional space requirements and computational cost, you should use DECIMAL only when you need exact results for fractional numbers, for example, when storing financial data. You can also use BIGINT to store financial data, avoiding both the imprecision of floating-point storage and the cost of the precise DECIMAL math.

### String Types

**VARCHAR and CHAR types**

The two major string types are VARCHAR and CHAR, which store character values.

VARCHAR stores **variable-length character strings** and is the most common string data type. It can require less storage space than fixed-length types, because it uses only as much space as it needs.

CHAR is fixed-length. Values are padded with spaces as needed for comparisons. CHAR is useful if you want to store **very short string**, or if all the **values are nearly the same length**. For example, CHAR is a good choice for MD5 values for user passwords. CHAR is also better than VARCHAR for data that's **changed frequently**, because a fixed-length row is not prone to fragmentation. For very short columns, CHAR is also more efficient than VARCHAR, because VARCHAR needs an extra length byte.

**BLOB and TEXT types**

BLOB and TEXT are string data types designed to store **large amounts of data** as either binary or character strings, respectively. 

Unlike with all other data types, MySQL handles each BLOB and TEXT value as an object with its own identity. Storage engines often store them specially. InnoDB may use a separate **external storage area** for them when they're large.

The only **difference** between the BLOB and TEXT families is that BLOB types store binary data with no collation or character set, but TEXT types store string data and that have a character set and collation (for sort).

MySQL sorts BLOB and TEXT columns differently from other types: it sorts only the first max_sort_length bytes of such columns. if you need to sort by only the first few characters, you can either decrease the max_sort_length or use ORDER BY SUBSTRING(column, length). 

MySQL can't index the full length of these data types and can't use the indexes for sorting.

**Using ENUM instead of a string type**

Sometimes you can use an ENUM column instead of conventional string types. An ENUM column can store a predefined set of distinct string values. MySQL stores them very compactly. It stores each value internally as an integer representing its position in the field definition list.

ENUM values sort by the internal integer values, not by the strings themselves. You can also use FIELD() to specify a sort order explicitly in your queries, but this prevents MySQL from using the index for sorting.

The biggest **disadvantage** of ENUM is that the list of strings is fixed, and adding or removing strings requires the use of `ALTER TABLE`. It might not be a good idea to use ENUM as a string data type when the list of allowed string values is likely to change arbitrarily in the future, unless it's acceptable to add them at the end of the list, which can be done without a full rebuild of the table.

Another disadvantage of ENUM values as integers and have to do a **lookup to convert** it to its string representation, that have some overhead. In particular, it can be slower to join a CHAR or VARCHAR column to an ENUM column than to another CHAR or VARCHAR column.

### Date and Time Types

MySQL has many types for various kinds of date and time values, such as YEAR and DATE. The finest granularity of time MySQL can store is one second. However, it can do temporal computations with microsecond granularity.

Most of date and time types have no alternatives, so there is no question of which one is the best choice. The only question is what to do when you need to store both date and time. MySQL offers two very similar data types for this purpose: DATETIME and TIMESTAMP. For many applications, either will work, but in some cases, one works better than the other.

DATETIME

DATETIME can stores values from the year 1001 to the year 9999, with a precision of one second. It stores the date and time packed into an integer in **YYYYMMDDHHMMSS** format, independent of time zone. This uses eight bytes of storage space.

It **is not** recommended to use a numeric column type (`INT`) to store datetime information. MySQL (and other systems too) provides many functions to handle datetime information. These functions are faster and more optimized than custom functions or calculations

TIMESTAMP

TIMESTAMP can stores the number of seconds **elapsed since** midnight, January 1, 1970, Greenwich Mean Time (GMT)--the same as a Unix timestamp. TIMESTAMP uses only four bytes of storage, so it has a much smaller range than DATETIME: from the year 1970 to the year 2038. MySQL provides the FROM_UNIXTIME() and UNIX_TIMESTAMP() functions to convert a Unix timestamp to a date, and vice versa.

The value a TIMESTAMP displays also depends on the time zone. The MySQL server, operating system, and client connections all have time zone settings.

TIMESTAMP also has special properties that DATETIME doesn't have. TIMESTAMP columns are NOT NULL by default, which is difference from every other data type.

In general you should use TIMESTAMP to store date and time, because it is more space-efficient than DATETIME.

Subsecond

If you need to store a date and time value with **subsecond**. MySQL concurrently does not have an appropriate data type for this, but you can use your own storage format: you can use BIGINT data type and store the value as a timestamp in microseconds, or you can use a DOUBLE and store the fractional part of the second after the decimal point. Or you can use MariaDB instead of MySQL.

### Bit-Packed Data Types

MySQL has a few data types that use individual bits within a value to store data compactly.

BIT

You can use a BIT column to store one or many true/false values in single column. BIT(1) defines a defines a field that contains a single bit, BIT(2) store 2 bits, and so on. The maximum length of a BIT column is 64 bits. For example, BIT(2) can store integer value 0~3, that represent two bits binary value 00, 01, 10, 11, respectively. 

MySQL treats BIT as a string type, not a numeric type. When you retrieve a BIT(1) value, the result is a string but the contents are the binary value 0 or 1, not the ASCII value "0" or "1". 

This data type very confused, so you should use BIT with caution. For most applications, it is a better idea to avoid this type. If you want to store a true/false value in a single bit of storage space, another option is to create a nullable CHAR(0) column. This column is capable of storing either the absence of a value (NULL) or a zero-length value (the empty string).

## Data Types Choice for Specific Usage

**Choosing Identifiers**

Choosing a good data type for an identifier column is very important.  You are more likely to compare these columns to other values (for example, in joins) and to use them for lookups than other columns. 

When choosing a type for an identifier column, you need to consider not only the storage type, but also how MySQL performs computations and comparisons on that type. For example, MySQL stores ENUM and SET types internally as integers but converts them to strings when doing comparisons in a string context.

You should use the same data types for identifier column in related tables. The types should match exactly, including properties such as UNSIGNED.

Choose the smallest size that can hold your required range of values.

Integer types are usually the best choice for identifiers, because they are fast and they work with AUTO_INCREMENT.

ENUM and SET types are generally a poor choice for identifiers.

String types. Avoid string types for identifiers if possible, because they take up a lot of space and are generally slower than integer types. 

You should also be very careful with completely random strings, such as those produced by MD5(), SHA1(), or UUID(). Each new value you generate with them will be distributed in arbitrary ways over a large space, which can slow INSERT because of random location in indexes and some types of SELET queries because of logically adjacent rows will be widely dispersed on disk and in memory.

If you do store UUID values, you should remove the dashes or, even better, convert the UUID values to 16-byte numbers with UNHEX() and store them in a binary(16) column. You can retrieve the values in hexadecimal format with the HEX() function. Values generated by UUID() have different characteristics from those generated by a cryptographic hash function such as SHA1() : the UUID values are unevenly distributed and are somewhat sequential. They’re still not as good as a monotonically increasing integer. 

Insertion operations

- Autoincrement integer: O(1)
- UUID: O(log n)

Query n rows by insertion order

- Autoincrement integer: O(1 * log n)
- UUID: O(n * log n)

Example of UUID store to and query by binary(16): 

```sql
insert into sometable (your_id) values (UNHEX(REPLACE("3f06af63-a93c-11e4-9797-00505690773f", "-", "")))
```

```sql
select * from sometable where your_id = UNHEX("3f06af63a93c11e4979700505690773f");
```

**IPv4 Address**

You should use INT UNSIGNED data type ((unsigned 32-bit integers) ) to store IP address. MySQL provides the INET_ATON() and INET_NTOA() functions to convert between the two representations.

```sql
INSERT INTO yourtable (ip_addr) values (INET_ATON('94.120.175.182'));
```

```sql
SELECT INET_NTOA(ip_addr) from yourtable;
```



## Schema Design Pitfalls in MySQL

There are also issues that arise from how MySQL is implemented, and that means you can make MySQL-specific mistakes, too. We need to avoid those mistakes and choose alternatives that work better with MySQL's specific implementation. 

Too many columns

MySQL's storage engine API works by copying rows between the server and the storage engine in a row buffer format. The server then decodes the buffer into columns. But it can be costly to turn the row buffer into the row data structure with the decoded columns. If you are planning for hundreds of columns, be aware that the server's performance characteristics will be a bit different.

Too many joins

Too many joins will be problematic in MySQL, because the cost of planning and optimizing the query is very expensive. MySQL has a limitation of 61 tables per join, and entity-attribute-value database require many self-joins. As a rough rule of thumb, it's better to have a dozen or fewer tables per query if you need queries to execute very fast with high concurrency.

The all-powerful ENUM

Beware of overusing ENUM. For example, 

```sql
CREATE TABLE ... (
	country enum('', '1', '2',..., '31')
```

This would be a questionable design decision in any database with an enumerated value type, because it really should be an integer that is foreign-keyed to a "dictionary" or "lookup" table anyway. Before MySQL 5.1 you can't add a new country to the list without an ALTER TABLE, which is a blocking operation, and even in 5.1 and newer if you add the value anywhere but at the end of the list.

The ENUM in disguise

An ENUM permits the column to hold one value from a set of defined values. A SET permits the column to hold one or more values from a set of defined values. Sometimes these can be easy to confuse. For example, 

```sql
CREATE TABLE ...(
	is_default set('Y', 'N') NOT NULL default 'N'
```

That almost surely ought to be an ENUM instead of a SET, assuming that it can't be both true and false at the same time.

NULL not invented here

There are some benefit of avoiding NULL, and indeed you  can consider alternatives when possible. Even when you do need to store a "no value" fact in a table, you might not need to use NULL. Perhaps you can use zero, a special value, or an empty string instead. 

To store a not null value is better performance, but sometimes it will confuse its meaning, complicate your code, and introduce bugs. Therefore you can take this to extremes. Don't be too afraid of using NULL when you need to represent an unknown value. In some cases, it's better to use NULL than a magical constant. Handling NULL isn't always easy, but it's often better than alternative, for example

```
CREATE TABLE ...(
	dt DATETIME NOT NULL DEFAULT '0000-00-00 00:00:00'
```

That bogus all-zeros value can cause lots of problems.

## Normalization and Denormalization

There are usually many ways to represent any given data, ranging from fully normalized to fully denormalized and anything in between. In a normalized database, each fact is represented once and only once. Conversely, in a denormalized database, information is duplicated, or stored in multiple places.

Storing duplicate data may cause inconsistency of data and need more storage space. Normalization is a way to avoid this problems.

**Pros and Cons of a Normalized Schema**

Advantages

- Normalized updates are usually faster than denormalized updates.
- Normalization makes your tables have little or no duplicated data, so there's less data to change.
- Normalized tables are usually smaller, so they fit better in memory and perform better.
- The lack of redundant data means there's less need for DISTINCT or GROUP BY queries when retrieving lists of values.

Disadvantages

- Any nontrivial query on a well-normalized schema will probably require at least one join, and perhaps several. This is not only expensive, but it can make some indexing strategies impossible.

**Pros and Cons of a Denormalized Schema**

A denormalized schema works well because everything is in the same table, which avoids joins. 

Advantages

- Avoids joins. If you don't need to join tables, the worst case for most queries, even the ones that don't use indexes is a full table scan. This can be much faster than a join when the data doesn't fit in memory, because it avoids random I/O.
- A single table can also allow more efficient indexing strategies.

**A Mixture of Normalized and Denormalized**

The truth is fully normalized and fully denormalized schemas are not fit the real world, you often need to mix the approaches, possibly using a partially normalized schema, cache tables, and other techniques.

Denomalization is more expensive to updates, because you have to change it in all tables. You must consider how frequently you'll have to make such changes and how long they will take, compared to how often you'll run the SELECT query.

## Cache and Summary Tables

Sometimes the best way to improve performance is to keep redundant data in the same table as the data from which it was derived. However, sometimes you'll need to build completely separate summary or cache tables, specially tuned for your retrieval needs. This approach works best if you can tolerate slightly stale data, but sometimes you really don't have a choice. For instance, when you need to avoid complex and expensive real-time updates.

**Cache tables** mean tables that contain data that can be easily retrieved from the schema. Some column data of tables are logically redundant. Cache tables are useful for optimizing search and retrieval queries. For example, you might need many different index combinations to speed up various types of queries. These conflicting requirements sometimes demand that you create a cache table that contains only some of the columns from the main table.

**Summary tables** mean tables that hold aggregated data from GOURP BY queries. The data that is not logically redundant. They also use the term "roll-up table". For example, it would be impossible to maintain an accurate real-time counter on a busy site. Instead, you could generate a summary table every hour. You can often do this with a single query, and it's more efficient than maintaining counters in real time. The drawback is that the counts are not 100% accurate.

When using cache and summary tables, you have to decide whether to maintain their data in real time or with periodic rebuilds. Which is better will depend on your application, but a periodic rebuild not only can save resource but also can result in a more efficient table that's not fragmented and has fully sorted indexes.

When you rebuild summary and cache tables, you'll often need their data to remain available during the operation. You can achieve this by using a "shadow table", which is a table you build behind the real table. When you're done building it, you can swap the tables with an atomic rename. For example

```sql
DROP TABLE IF EXISTS my_summary_new, my_summary_old;
CREATE TABLE my_summary_new LIKE my_summary;
RENAME TABLE my_summary TO my_summary_old, my_summary_new TO my_summary;
```

**Materialized Views**

View are a logical virtual table created by "select query" but the result is not stored anywhere in the disk and every time we need to fire the query when we need data, so always we get updated or latest data from original tables. 

Materialized views are also the logical view of our data-driven by the select query but the result of the query will get stored in the table or disk, also the definition of the query will also store in the database.

MySQL doesn't support this natively. However, you can implement materialized view yourself, using Justin Swanhart's open source Flexviews tools.

**Counter Tables**

An application that keeps counts in a table can run into concurrency problems when updating the counters. Such tables are very common in web applications. You can use them to cache the number friends a user has, the number of downloads of a file, and so on. It's often a good idea to build a separate table for the counters, to keep it small and fast.

```sql
CREATE TABLE hit_counter (
	cnt int unsigned not null
) ENGINE=InnoDB;
UPDATE hit_counter SET cnt = cnt + 1;
```

```sql
CREATE TABLE hit_counter (
	slot tinyint unsigned not null primary key,
    cnt int unsigned not null
) ENGINE=InnoDB;
UPDATE hit_counter SET cnt = cnt + 1 WHERE slot = RAND() * 100;
SELECT SUM(cnt) FROM hit_counter;
```



## Speeding Up Alter Table

MySQL's ALTER TABLE performance can become a problem with very large tables. MySQL performs most **alterations** by making an empty table with the desired new structure, inserting all the data from the old table into the new one, and deleting the old table. This can take a very long time, especially if you're short on memory and the table is large and has lots of indexes.

In general, most ALTER TABLE operations will cause interruption of service in MySQL. For the general case, you need to use either operational **tricks** such as swapping servers around and performing the ALTER on servers that are not in production service, or a shadow copy approach. Tools can help with to build a new table for a shadow copy. For example, the "online schema change" tools from Facebook's database operations team, Shlomi Noach's openark toolkit, and Percona Toolkit.

Not all ALTER TABLE operations cause table rebuilds. For example, change or drop a column's default value in two ways (one fast, and one slow).

```sql
ALTER TABLE your_table
MODIFY COLUMN your_column TINYINT(3) NOT NULL DEFAULT 5;
```

The following code is a fast way to modify column's default value. The default value for the column is actually stored in the table's .frm file, so you should be able to change it without touching the table itself. Any MODIFY COLUMN will cause a table rebuild, but you can use ALTER COLUMN to change column's default value.

```sql
ALTER TABLE your_table
ALTER COLUMN your_column SET DEFAULT 5;
```



## Conclusion

The **general principles** for choosing a data type are introduced earlier in this article. They are "smaller is usually better", "simple is good", and "avoid NULL if possible".

- Sometimes, the data type of a column doesn't limit one kind of data type, so we can use the principle "simple is good" to determine what kind of data type we use.
- When we determine what kind of data type for the column, we can follow the principle of "smaller is usually better" to choose a specific data type. Then, we also can follow the principle "avoid NULL if possible" to determine does the column allows NULL.

The kinds of data types are whole numbers, real numbers, strings (text), binary, temporal (time), and others. For **choosing a specific data type** from a kind of data type, you can use the following methods.

- For choosing **a data type of whole numbers**. 1. First, you should choose the **less** length of integer type if possible. 2. If you don't there are no negative numbers, you should set it to be **unsigned** (that makes values range larger). 3. For storing **bool data**, the better choice is BOOL(TINYINT) data type, others choices are BIT(1), CHAR(0). 4. There are many common usage scenarios of whole numbers. For example, table's identifiers, counter numbers. For **identifiers** of tables, integers are the best choice for identifiers, and all tables' identifiers should be the same integer type.
- For choosing **a data type of real numbers**. 1. If you need **exact calculations** you should choose the DECIMAL type. 2. If you can accept **approximate calculations** you can choose the FLOAT and DOUBLE data type by different precisions. 3. Note that you should use DECIMAL only when you need exact results for fractional numbers because it needs more cost than floating numbers. 4. There are many common usage scenarios of real numbers, for example, financial data. For **financial data**, the DECIMAL type is a better choice for representing financial data, but if the financial data have small limit precision you can convert all data to integer values and use BIGINTEGER to store them.
- For choosing **a data type of temporal types**. 1. Sometimes you can use integers to replace temporal types because integer types are simpler and faster than temporal types, but you will lose lots of very convenient built-in functions with temporal types, such as `WEEK()`, `ADDDATE()`.  2. In the choice of temporal types, the most common question is how to choose a data type that contains both date and time types from DATETIME and TIMESTAMP. The main differences between DATETIME and TIMESTAMP are representing range, space cost, and affect by time zone.
- For choosing a specific string type. 1. The most choice are VARCHAR and CHAR. When your column values limit by small length, you can use CHAR to store it, otherwise use VARCHAR. 2. When you need to store large amounts of characters, you can choose a text type from text family types according to your range of characters of text. 3. For binary data, the basic choices are BINARY and VARBINARY that same with CHAR and VARCHAR, but store binary. 4. When you want to store large amounts of binary, you can choose a binary type from blob family types. 5. There are some special data types in string types, such as ENUM, SET, and JSON. 6. There are many common usage scenarios of string types, for example, ID card No., account number, name, URI, and so on. 7. Note that sometimes you can use integers to replace strings it has much performance improvement, such as IPv4 address can use BIGINTEGER to represent.

Comparison and Sorting

- Same data types comparison is faster than different data types.
- Numeric data types comparison is faster than character data types.

|                       | comparison and sorting        |
| --------------------- | ----------------------------- |
| INT or DOUBLE         | Numeric value                 |
| BINARY                | Numeric value (bytes)         |
| DATETIME or TIMESTAMP | Numeric value (Integer value) |
| CHAR or VARCHAR       | Characters                    |

Beware of Autogenerated Schemas

Autogenerate schemas can cause severe performance problems. Some programs use large VARCHAR fields for everything, or use difference data types for columns that will be compared in joins. Be sure to double-check a schema if it was created for you automatically.

## Appendix

I. Basic Information of Data Types

| data type                               | Storage                                                      | Values Range                                                 | Note                                                         |
| --------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| BIT(M)                                  | 1~64 bit, approximately (M+7)/8 bytes (Fixed space cost specified by M) | binary value (0~2^64-1)                                      | MySQL treats BIT as a string type, not a numeric type. This data type very confused, so you should use BIT with caution. |
| TINYINT(M)                              | 1 byte                                                       | Signed: -128\~127, Unsigned: 0\~255                          | M doesn't limit length of number, it just for client display format. |
| BOOL, BOOLEAN (TINYINT(1))              | 1 byte                                                       | Signed: -128\~127, Unsigned: 0\~255                          |                                                              |
| SMALLINT(M)                             | 2 bytes                                                      | Signed: -32768\~32767, Unsigned: 0\~65535                    |                                                              |
| MEDIUMINT(M)                            | 3 bytes                                                      | Signed: -8388608\~8388607, Unsigned: 0\~16777215             |                                                              |
| INT(M), INTEGER(M)                      | 4 bytes                                                      | Signed: -2147483648\~2147483647, Unsigned: 0\~4294967295     |                                                              |
| BIGINT(M)                               | 8 bytes                                                      | Signed: -2^63\~2^63-1, Unsigned: 0\~2^64 -1                  |                                                              |
| FLOAT(M, D)                             | 4 bytes                                                      | Approximately 7 decimal places. Unsigned means to disallow negative values |                                                              |
| DOUBLE(M, D)                            | 8 bytes                                                      | Approximately 15 decimal places. Unsigned means to disallow negative values |                                                              |
| DECIMAL(M, D), (DEC, NUMERIC, FIXED)    | Length + 1 or 2 bytes (Fixed space cost specified by M)      | M: 1\~65, D: 0\~30. Unsigned means to disallow negative values |                                                              |
| YEAR                                    | 1 byte                                                       | 1901 to 2155                                                 |                                                              |
| DATE                                    | 3 bytes                                                      | '1000-01-01' to '9999-12-31'                                 |                                                              |
| TIME [fsp]                              | 3 bytes, (Since MySQL 5.6.4, it's 3 bytes + fractional seconds storage) | '-838:59:59.000000' to '838:59:59.000000'                    | fsp: fractional seconds precision, Range of fsp: 0~6         |
| DATETIME [fsp]                          | 8 bytes, (Since MySQL 5.6.4, it's **5 bytes** + fractional seconds storage) | '1000-01-01 00:00:00.000000' to '9999-12-31 23:59:59.999999' | fsp: fractional seconds precision, Range of fsp: 0~6         |
| TIMESTAMP [fsp]                         | 4 bytes, (Since MySQL 5.6.4, it's 4 bytes + fractional seconds storage) | '1970-01-01 00:00:01.000000' UTC to '2038-01-19 03:14:07.999999' UTC | fsp: fractional seconds precision, Range of fsp: 0~6         |
| CHAR(M) [charset] [collate]             | M: 0~255 characters, (Fixed space cost specified by M)       |                                                              |                                                              |
| VARCHAR(M)  [charset] [collate]         | M: 0~65535 characters (Variable space cost and max specified by M), have length prefix (1byte or 2bytes) |                                                              |                                                              |
| TINYTEXT [charset] [collate]            | 0~255(2^8-1) characters, (Variable space cost and max limit by 255 chars cost), 1 byte length prefix |                                                              |                                                              |
| TEXT(M) [charset] [collate]             | max 65535(2^16-1) characters (Variable space cost and max limit by 65535 chars cost), 2 byte length prefix |                                                              | M means smallest numbers of characters                       |
| MEDIUMTEXT [charset] [collate]          | max 16,777,215 (2^24 − 1) characters (Variable space cost) , 3 bytes length prefix |                                                              |                                                              |
| LONGTEXT [charset] [collate]            | max 4,294,967,295 or 4GB (2^32 − 1) characters (Variable space cost), 4 bytes length prefix |                                                              |                                                              |
| BINARY(M)                               | M: 0~255 bytes,  (Fixed space cost specified by M). (like CHAR) |                                                              |                                                              |
| VARBINARY(M)                            | M: 0~65535 bytes (Variable space cost and max specified by M). (like VARCHAR) |                                                              |                                                              |
| TINYBLOB                                | max 255(2^8-1) bytes (Variable space cost and max limit by 255 bytes), 1 byte length prefix |                                                              |                                                              |
| BOLB(M)                                 | max 65535 (2^16-1) bytes (Variable space cost and max limit by 65535 bytes), 2 bytes length prefix |                                                              | M means smallest numbers of bytes                            |
| MEDIUMBLOB                              | max 16,777,215 (2^24 − 1) bytes (Variable space cost), 3 bytes length prefix |                                                              |                                                              |
| LONGBLOB                                | max 4,294,967,295 or 4GB (2^32 − 1) bytes (Variable space cost), 4 bytes length prefix |                                                              |                                                              |
| ENUM('value1', ...) [charset] [collate] | less than 3000 list of values (Variable space cost), internally as integers |                                                              |                                                              |
| SET('value1', ...) [charset] [collate]  | max 64 distinct members (Variable space cost), internally as integers |                                                              |                                                              |
| JSON                                    | roughly same as LONGTEXT, limit by max_allowed_packet system variable. |                                                              |                                                              |

Fractional Seconds Precision Storage (Since MySQL 5.6.4)

| Precision | Storage Required |
| --------- | ---------------- |
| 0         | 0 bytes          |
| 1, 2      | 1 bytes          |
| 3, 4      | 2 bytes          |
| 5, 6      | 3 bytes          |




## References

[1] High Performance MySQL by Baron Schwartz, Vadim Tkachenko, Peter Zaitsev, Derek J. Balling

[2] MySQL 5.7 Reference Manual