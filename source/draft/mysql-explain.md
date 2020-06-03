Title: MySQL explain

## Meaning Key of Columns in explain result

### id

### select_type

- SIMPLE: Simple [`SELECT`](https://dev.mysql.com/doc/refman/8.0/en/select.html) (not using [`UNION`](https://dev.mysql.com/doc/refman/8.0/en/union.html) or subqueries)
- PRIMARY
- UNION
- DEPENDENT UNION
- UNION RESULT
- SUBQUERY
- DEPENDENT SUBQUERY
- DERIVED
- DEPENDENT DERIVED
- MATERIALIZED: Materialized subquery. **Subquery** materialization uses an in-memory temporary table when possible, falling back to on-disk storage if the table becomes too large. See Section 8.4. 4, “Internal Temporary Table Use in **MySQL**”. If materialization is not used, the optimizer sometimes rewrites a noncorrelated **subquery** as a correlated **subquery**.
- UNCACHETABLE SUBQUERY
  UNCACHETABLE UNION

`DEPENDENT` typically signifies the use of a correlated subquery. See [Section 13.2.11.7, “Correlated Subqueries”](https://dev.mysql.com/doc/refman/8.0/en/correlated-subqueries.html).



### table

table names

### partitions

### type

Describes how tables are joined. (Access methods to find and return rows)

- **system**
- **const**: The table has at most one matching row, which is read at the start of the query. e.g `SELECT * FROM *tbl_name* WHERE *primary_key*=1;`
- **eq_ref**: One row is read from this table for each combination of rows from the previous tables. It is used when all parts of an index are used by the join and the index is a `PRIMARY KEY` or `UNIQUE NOT NULL` index.
- **ref**: All rows with matching index values are read from this table for each combination of rows from the previous tables.
- **fulltext**: The join is performed using a `FULLTEXT` index. 
- **ref_or_null**: This join type is like `ref`, but with the addition that MySQL does an extra search for rows that contain `NULL` values.
- **index_merge**: This join type indicates that the Index Merge optimization is used. In this case, the `key` column in the output row contains a list of indexes used. Indicates a query to make limited use of multiple indexes from a single table. For example, the film_actor table has an index on film_id and an index on actor_id, the query is `SELECT film_id, actor_id FROM sakila.film_actor WHERE actor_id = 1 OR film_id = 1`
- **unique_subquery**: This type replaces [`eq_ref`](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#jointype_eq_ref) for some `IN` subqueries of the following form. `value IN (SELECT primary_key FROM single_table WHERE some_expr)`
- **index_subquery**: This join type is similar to [`unique_subquery`](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#jointype_unique_subquery). It replaces `IN` subqueries, but it works for nonunique indexes in subqueries. `value IN (SELECT key_column FROM single_table WHERE some_expr)`
- **range**: Only rows that are in a given range are retrieved, using an index to select the rows.
- **index**: 1) MySQL plans to scan an index. e.g select all rows of a column that contains in index. `SELECT column_from_key from test`. 2) A full table scan is performed using reads from the index to look up data rows in index order.
- **ALL**: a full table scan is done for each combination of rows from the previous tables. When type is `ALL` in EXPLAIN, the `key` will be null in EXPLAIN.

Cost Time order by how much data does MySQL require reading to execute the query: `ALL` > `index` > `range` > `unique_subquery` > `ref` > `const`

### possible_keys

your index names

### key

your index names

### key_len

### ref

- const

### rows

### filtered

### Extra

- Using index (only show): it means storage engine use covering index, or fetch all data from index file rather than physical rows.
- Using index condition:
- Using where: it means the MySQL server is applying a WHERE filter after the storage engine returns the rows.

## References

[1] [EXPLAIN Output Format - MySQL 8.0 Documentation](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#explain-join-types)