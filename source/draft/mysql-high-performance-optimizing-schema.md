Title: MySQL 优化（一）: Optimizing Schema

name: `mysql`-high-performance-optimizing-schema

Content

- Choosing Optimal Data Types
- Schema Design Problems in MySQL
- //Normalization and Denormalization
- //Cache and Summary Tables
- //Speeding Up Alter Table
- Conclusion
- References

Good logical and physical design is the cornerstone of high performance, and you must design your schema for the specific queries you will run. This often involves trade-offs. For example, a denormalized schema can speed up some types of queries but slow down
others. Adding counter and summary tables is a great way to optimize queries, but they
can be expensive to maintain.

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

DATETIME can stores values from the year 1001 to the year 9999, with a precision of one second. It stores the date and time packed into an integer in YYYYMMDDHHMMSS format, independent of time zone. This uses eight bytes of storage space.

TIMESTAMP can stores the number of seconds elapsed since midnight, January 1, 1970, Greenwich Mean Time (GMT)--the same as a Unix timestamp.

### Bit-Packed Data Types

### Choosing Identifiers

### Special Types

## Schema Design Problems in MySQL

...

## //Normalization and Denormalization

...

## //Cache and Summary Tables

...

## //Speeding Up Alter Table

...

## Conclusion

(0. Basic conclusion. 1. Numeric, String ,date time types, and special types choose methods. 2. Common usage scenario of a data types choose methods. For example, financial data.)

## Others

Suggestions of data types choice

- Storing dates and times data using MySQL's built-in types instead of as strings.
- Using integers for IP addresses.



## References

[1] High Performance MySQL by Baron Schwartz, Vadim Tkachenko, Peter Zaitsev, Derek J. Balling