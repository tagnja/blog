Title: MySQL 优化（一）: Optimizing Schema

name: `mysql`-high-performance-optimizing-schema

Content

- Choosing Optimal Data Types
- Schema Design Problems in MySQL
- Normalization and Denormalization
- Cache and Summary Tables
- Speeding Up Alter Table
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

The FLOAT and DOUBLE types support approximate calculations with standard floating point math. The DECIMAL type is for storing exact fractional numbers. it supports exact math.

Floating-point math is significantly faster than decimal math, because the CPU performs the computations natively.

Both floating-point and DECIMAL types let you specify a precision. For a DECIMAL column, you can specify the maximum allowed digits and the decimal point.

### String Types

varchar and char types

BLOB and TEXT types

Using ENUM instead of a string type

### Date and Time Types

### Bit-Packed Data Types

### Choosing Identifiers

### Special Types

## Schema Design Problems in MySQL

...

## Normalization and Denormalization

...

## Cache and Summary Tables

...

## Speeding Up Alter Table

...

## Others

Suggestions of data types choice

- Storing dates and times data using MySQL's built-in types instead of as strings.
- Using integers for IP addresses.

## References

[1] High Performance MySQL by Baron Schwartz, Vadim Tkachenko, Peter Zaitsev, Derek J. Balling