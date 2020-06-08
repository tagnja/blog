Title: MySQL explain

The EXPLAIN command is the main way to find out how the query optimizer decides to execute queries. This feature has limitations and doesn't always tell the truth, but its output is the best information available. Interpreting EXPLAIN will also help you learn how MySQL's optimizer works.

## Invoking EXPLAIN

To use EXPLAIN, simply add the word EXPLAIN just before the SELECT keyword in your query. MySQL will set a flag on the query. When it executes the query, the flag cases it to return information about each step in the execution plan, instead of executing it. It returns one or more rows, which show each part of the execution plan and the order of execution. There is a simple example of EXPLAIN query statement:

```sql
EXPLAIN SELECT 1;
```

There is one row in the output per table in the query. If the query joins two tables, there will be two rows of output. The meaning of "table" is fairly broad meaning: it can mean a subquery, a UNION result, and so on.

It's a common mistake that MySQL doesn't execute a query when you add EXPLAIN to it. In fact, if the query contains a subquery in the FROM clause, MySQL actually executes the subquery, places its results into a temporary table, and finishes optimizing the outer query. It has to process all such subqueries before it can optimize the outer query fully, which it must do for EXPLAIN. This means EXPLAIN can actually cause a great deal of work for the server if the statement contains expensive subqueries or views that use the TEMPTABLE algorithm.

EXPLAIN is an approximation. Sometimes it's a good approximation, but at other times, it can be very far from the truth. Here are some of its limitation:

- EXPLAIN doesn't tell you anything about how triggers, stored functions, or UDFs will affect your query.
- It doesn't work for stored procedures.
- It doesn't tell you about optimization MySQL does during query execution.
- Some of the statistics it shows are estimates and can be very inaccurate.
- It doesn't show you everything there is to know about a query's execution plan.
- It doesn't distinguish between some things with the same name. For example, it uses "filesort" for in-meory sorts and for temporary files, and it displays "Using temporary" for temporary tables on disk and in memory.

**Rewriting Non-SELECT Queries**

MySQL explain only SELECT queries, not stored routine calls or INSERT, UPDATE, DELETE, or any other statements. However, you can rewrite some non-SELECT queries to be EXPLAIN-able. To do this, you just need to convert the statement into an equivalent SELECT that accesses all the same columns. Any columns mentioned must be in a SELECT list, a join clause, or a WHERE clause. Note that since MySQL 5.6, it can explain INSERT, UPDATE, and DELETE statements.

For example:

```sql
UPDATE actor 
INNER JOIN film_actor USING(actor_id)
SET actor.last_update=film_actor.last_update;
```

To

```sql
EXPLAIN SELECT actor.last_update, film_actor.last_update 
FROM actor 
INNER JOIN film_actor USING(actor_id)
```



## The Columns in EXPLAIN

EXPLAIN's output always has the same columns, which adds a `filtered` column in MySQL 5.1.

Keep in mind that the rows in the output come in the order in which MySQL actually executes the parts of the query, which is not always the same as the order in which they appear in the original SQL. In EXPLAIN of MySQL, there are columns: id, select_type, table, partitions, type, possible_keys, key_len, ref, rows, filtered, Extra.

**The id Column**

The column always contains a number, which identifies the SELECT to which the row belongs.

MySQL divides SELECT queries into simple and complex types, and the complex types can be grouped into three broad classes: simple subqueries, derived tables (subqueries in the FROM clause), and UNIONs. Here is a simple subquery example:

```sql
EXPLAIN SELECT (SELECT 1 FROM sakila.actor LIMIT 1) FROM sakila.film;
```

```
+----+-------------+-------+...
| id | select_type | table |...
+----+-------------+-------+...
| 1  | PRIMARY     | film  |...
| 2  | SUBQUERY    | actor |...
+----+-------------+-------+...
```

Here is a derived tables query example:

```sql
EXPLAIN SELECT film_id FROM (SELECT film_id FROM sakila.film) AS der;
```

```
+----+-------------+------------+...
| id | select_type | table      |...
+----+-------------+------------+...
| 1  | PRIMARY     | <derived2> |...
| 2  | DERIVED     | film       |...
+----+-------------+------------+...
```

A UNION query example: 

```sql
EXPLAIN SELECT 1 UNION ALL SELECT 1;
```

```
+------+--------------+------------+...
| id   | select_type  | table      |...
+------+--------------+------------+...
| 1    | PRIMARY      | NULL       |...
| 2    | UNION        | NULL       |...
| NULL | UNION RESULT | <union1,2> |...
+------+--------------+------------+...
```

UNION results are always placed into an anonymous temporary table, and MySQL then reads the results back out of the temporary table. The temporary table doesn't appear in the original SQL, so its id columns is NULL. 

**The select_type Column**

This column shows whether the row is a simple or complex SELECT (and if it's the latter, which of the three complex types it it). The value `SIMPLE` means the query contains no subqueries or UNIONs. If the the query has any such complex subparts, the outermost part is labeled `PRIMARY`, and other parts are labeled as follows:

- `SUBQUERY`. A SELECT that is contained in a subquery in the SELECT clause (not in the FROM clause) is labeled SUBQUERY.
- `DERIVED`. A SELECT that is contained in a subquery in the FROM clause. The server refers to this as a "derived table" internally, because the temporary table is derived from the subquery.
- `UNION`. The second and subsequent SELECTs in a UNION are labeled as UNION. The first SELECT is labeled PRIMARY as though it is executed as part of the outer query.
- `UNION RESULT`. The SELECT used to retrieve results from the UNION's anonymous temporary table is labeled as UNION RESULT.

In addition to these values, a SUBQUERY and a UNION can be labeled as DEPENDENT and UNCACHEABLE. DEPENDENT means the SELECT depends on data that is found in an outer query; UNCACHEABLE means something in the SELECT prevents the result form being cached with an Item_cache (such as the RAND() function).

UNCACHEABLE UNION example:

```sql
EXPLAIN select tmp.id 
FROM (SELECT * FROM test t1 WHERE t1.id=RAND() 
     UNION ALL 
	SELECT * FROM test t2 WHERE t2.id=RAND()) AS tmp;
```

```
+----+-------------------+------------+...
| id | select_type       | table      |...
+----+-------------------+------------+...
| 1  | PRIMARY           | <derived2> |...
| 2  | DERIVED           | t1         |...
| 3  | UNCACHEABLE UNION | t2         |...
+----+-------------------+------------+...
```

**The table Column**

This column shows which table the row is accessing. It's the table, or its alias. The number of the rows in EXPLAIN equals the sum of number of SELECT, JOIN and UNION.

MySQL's query execution plans are always left-deep trees. The leaf nodes in order correspond directly to the rows in EXPLAIN. For example: 

```sql
EXPLAIN SELECT film.film_id
FROM sakila.film
	INNER JOIN sakila.film_actor USING(film_id)
	INNER JOIN sakila.actor USING(actor_id);
```

```
+----+-------------+------------+...
| id | select_type | table      |...
+----+-------------+------------+...
| 1  | SIMPLE      | actor      |...
| 1  | SIMPLE      | film_actor |...
| 1  | SIMPLE      | film       |...
+----+-------------+------------+...
```

<img src="mysql-explain-table-column-ordering.png" alt="table order">

Derived tables and unions

The table column becomes much more complicated when there is a subquery in the FROM clause or a UNION. In these cases, there really isn't a "table" to refer to, because the anonymous temporary table MySQL creates exists only while the query is executing.

When there's a subquery in the FROM clause, the table column is of the form `<derivedN>`, where N is the subquery's id. This always a "forward reference". In other words, N refers to a later row in the EXPALIN output.

When there's a UNON, the UNION RESULT table column contains a list of ids that participate in the UNION. This is always a "backward reference", because the UNION RESULT comes after all of the rows that participate in the UNION.

Example of a complex SELECT types:

```sql
1 EXPLAIN
2 SELECT actor_id,
3 	(SELECT 1 FROM sakila.film_actor WHERE film_actor.actor_id =
4 		der_1.actor_id LIMIT 1)
5 FROM (
6 	SELECT actor_id
7 	FROM sakila.actor LIMIT 5
8 ) AS der_1
9 UNION ALL
10 SELECT film_id,
11 	 (SELECT @var1 FROM sakila.rental LIMIT 1)
12 FROM (
13 	 SELECT film_id,
14 		(SELECT 1 FROM sakila.store LIMIT 1)
15 	 FROM sakila.film LIMIT 5
16 ) AS der_2;
```

```
+------+----------------------+------------+...
| id   | select_type          | table      |...
+------+----------------------+------------+...
| 1    | PRIMARY              | <derived3> |...
| 3    | DERIVED              | actor      |...
| 2    | DEPENDENT SUBQUERY   | film_actor |...
| 4    | UNION                | <derived6> |...
| 6    | DERIVED              | film       |...
| 7    | SUBQUERY             | store      |...
| 5    | UNCACHEABLE SUBQUERY | rental     |...
| NULL | UNION RESULT         | <union1,4> |...
+------+----------------------+------------+...
```

Reading EXPLAIN's output often requires you to jump forward and backward in the list. 

**The type Column**

The MySQL manual says this column shows the "join type", but it's more accurate to say the access type. In other words, how MySQL has decided to find rows in the table. Here are the most important access methods, from worst to best:

- ALL. This means a table scan. MySQL must scan through the table from beginning to end to find the row. (There are exceptions, such as queries with LIMIT or queries that display "Using distinct/not exist" in the Extra column.)
- index. This means an index scan. The main advantage is that this avoids sorting. The biggest disadvantage is the cost of reading an entire table in index order. This usually means accessing the rows in random order, which is very expensive. If you also see "Using index" in the Extra column, it mean MySQL is using a covering index. This is much less expensive than scanning the table in index order.
- range. A range scan is a limited index scan. It begins at some point in the index and returns rows that match a range of values. This is better than a full index scan because it doesn't go through the entire index. Obvious range scans are queries with a BETWEEN or > in the WHERE clause.
- ref. This is an index access (or index lookup) that returns rows that match a single value. The `ref_or_null` access type is a variation on `ref`.  It means MySQL must do a second lookup to find NULL entries after doing the initial lookup.
- eq_ref. This is an index lookup that MySQL knows will return at most a single value. You will see this access method when MySQL decides to use a primary key or unique index to satisfy the query by comparing it to some reference value.
- const, system. MySQL uses these access types when it can optimize away some part of the query and turn it into a constant. The table has at most one matching row, which is read at the start of the query. For example, if you select a row's primary key by placing it primary key into then where clause. e.g `SELECT * FROM <table_name> WHERE <primary_key_column>=1;`
- NULL. This access method means MySQL can resolve the query during the optimization phase and will not even access the table or index during the execution stage. For example, selecting the minimum value from an indexed column can be done by looking at the index alone and requires no table access during execution.

Other types

- fulltext. The join is performed using a `FULLTEXT` index. 
- index_merge. This join type indicates that the Index Merge optimization is used. In this case, the `key` column in the output row contains a list of indexes used. Indicates a query to make limited use of multiple indexes from a single table. For example, the film_actor table has an index on film_id and an index on actor_id, the query is `SELECT film_id, actor_id FROM sakila.film_actor WHERE actor_id = 1 OR film_id = 1`
- unique_subquery. This type replaces [`eq_ref`](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#jointype_eq_ref) for some `IN` subqueries of the following form. `value IN (SELECT primary_key FROM single_table WHERE some_expr)`
- index_subquery. This join type is similar to [`unique_subquery`](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#jointype_unique_subquery). It replaces `IN` subqueries, but it works for nonunique indexes in subqueries. `value IN (SELECT key_column FROM single_table WHERE some_expr)`

**The possible_keys Column**

This column shows which indexes could be used for the query, based on the columns the query accesses and the comparison operators used. This list is created early in the optimization phase, so some of the indexes listed might be useless for the query after subsequent optimization phases.

**The key Column**

This column shows which index MySQL decided to use to optimize the access to the table. If the index doesn't appear in `possible_keys`, MySQL chose it for another reason--for example, it might choose a covering index even when there is no WHERE clause.

In other words, `possible_keys` reveals which indexes can help make row lookups efficient, but `key` shows which index the optimizer decided to use to minimize query cost.

Here's an example:

```sql
EXPLAIN SELECT actor_id, film_id FROM sakila.film_actor;
```

```
*************************** 1. row ***************************
id: 1
select_type: SIMPLE
table: film_actor
type: index
possible_keys: NULL
key: idx_fk_film_id
key_len: 2
ref: NULL
rows: 5143
Extra: Using index
```

**The key_len Column**

This column shows the number of bytes MySQL will use in the index. If MySQL is using only some of the index's columns, you can use this value to calculate which columns it uses. Remember that MySQL 5.5 and older versions can use only the left most prefix of the index. For example, file_actor's primary key covers two SMALLINT columns, and a SMALLINT is two bytes, so each tuple in the index is four bytes. Here's a query example:

```sql
EXPLAIN SELECT actor_id, film_id FROM sakila.film_actor WHERE actor_id=4;
```

```
...+------+---------------+---------+---------+...
...| type | possible_keys | key    | key_len  |...
...+------+---------------+---------+---------+...
...| ref  | PRIMARY       | PRIMARY | 2       |...
...+------+---------------+---------+---------+...
```

You can using EXPLAIN to get how much byte in one row of the index, then to calculate the key_len of the EXPLAIN of the query uses how much rows index.

MySQL doesn't always show you how much of an index is really being used. For example, if you perform a LIKE query with a prefix pattern match, it will show that the full width of the column is being used.

The key_len column shows the maximum possible length of the indexed fields, not the actually number of bytes the data in the table used.

**The ref Column**

This column shows which columns or constant from preceding tables are being used to look up values in the index named in the key column.

**The rows Column**

This column shows the number of rows MySQL estimates it will need to read to find the desired rows. 

Remember, this is the number of rows MySQL thinks it will examine, not the number of rows in the result set. Also realize that there are many optimizations, such as join buffers and caches, that aren't factored into the number of rows shown.

**The filtered Column**

This column shows a pessimistic estimate of the percentage of rows that will satisfy some condition on the table, such as a WHERE clause or a join condition. If you multiply the rows column by this percentage, you will see the number of rows MySQL estimates it will join with the previous tables in the query plan.

**The Extra Colum**

This column contains extra information that doesn't fit into other columns. The most important values you might frequently are as follows:

- Using index. This indicates that MySQL will use a covering index to avoid accessing the table.
- Using where. This means the MySQL server will post-filter rows after the storage engine retrieves them. 
- Using temporary. This means MySQL will use a temporary table while sorting the query's result.
- Using filesort. This means MySQL will use an external sort to order the results, instead of reading the rows from the table in index order. MySQL has two filesort algorithms. Either type can be done in memory or on disk. EXPLAIN doesn't tell you which type of filesort MySQL will use, and it doesn't tell you whether the sort will be done in memory or on disk.
- Range checked for each record (index map:N). This value means there's no good index, and the indexes will be reevaluated for each row in a join. N is a bitmap of the indexes shown in possible_keys and is redundant. 
- Using index condition: Tables are read by accessing index tuples and testing them first to determine whether to read full table rows.

**Improvements in MySQL 5.6**

Some EXPLAIN improvements in MySQL 5.6

- To explain queries such as UPDATE, INSERT, and so on.
- A variety of improvements to the query optimizer and execution engine that allow  anonymous temporary tables to be materialized as late as possible, rather than always creating and filling them before optimizing and executing the portions of the query that refer to them. This will allow MySQL to explain queries with subqueries instantly, without having to actually execute the subqueries first.



## Conclusion

The most important columns of EXPLAIN are: type and Extra. They determines does the query uses a index or covering index.

the most important access methods (type of EXPLAIN), from worst to best: 
- ALL
- index
- range
- ref
- eq_ref
- const, system
- NULL

## References

[1] High Performance MySQL by Baron Schwartz, Vadim Tkachenko, Peter Zaitsev, Derek J. Balling

[2] [EXPLAIN Output Format - MySQL 8.0 Documentation](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#explain-join-types)