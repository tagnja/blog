title: MySQL 优化（三）：Query

Content

- Slow Query Basic
- Ways to Restructure Queries
- Query Execution Basics
- Limitations of the MySQL Query Optimizer
- Query Optimizer Hints
- Optimizing Specific Types of Queries
- Conclusion
- Appendixes
- References

This post is talking about MySQL query optimization, if you are familiar with this topic, you can go to the conclusion part that's a good summary of the query optimization in MySQL that may give you some enlightenment.

Schema optimization and indexing, which are necessary for high performance. But they are not enough, you also need to design your queries well. If your queries are bad, even the best-designed schema and indexes will not perform well.

We need to understand deeply how MySQL really executes queries, so you can reason about what is efficient or inefficient, exploit MySQL's strengths, and avoid its weakness.

**Why Are Queries Slow**

Before trying to write fast queries, you need to know the good or bad queries are determined by response time. Queries are tasks, but they are composed of subtasks, and those subtasks consume time. To optimize a query, you must optimize its subtasks by eliminating them, make them happen fewer times, or making them happen more quickly.

You can use profiling a query to find out what subtasks performs to execute a query, and which ones are slow. In general, you can think of a query's lifetime by following the query through its sequence from the client to the server, where it is parsed, planned, and executed, and then back again to the client. Execution is one of the most important stages in a query's lifetime. It involves lots of calls to the storage engine to retrieve rows, as well as post-retrieval operations such as grouping and sorting.

While accomplishing all these tasks, the query spends time on the network, in the CPU, in operations (such as statistics and planning, locking, and call the storage engine to retrieve rows).

In every case, excessive time may be consumed because the operations are performed needless, performed too many times, or are too slow. The goal of optimization is to avoid that, by eliminating or reducing operations, or making them faster. For do this optimization, we need to understanding a query's lifecycle and thinking in terms of where the time is consumed.

## Slow Query Basic: Optimize Data Access

The most basic reason a query doesn't perform well is because it's working with too much data. We can use the following methods to find out whether your query is access to more data than it needs.

- Find out whether your application is retrieving more data than you need. That means it's accessing too many rows, or accessing too many columns.
- Find out whether the MySQL server is analyzing more rows than it needs.

### Are You Asking the Database for Data You Don't Need

Some query ask for more data than they need and then throw some of it away. This demands extra work of the MySQL server, adds network overhead, and consumes memory and CPU resources on the application server.

The common mistakes of fetching more data

- Fetching more rows than needed.
- Fetching all columns from a multi-table join.
- Fetching all columns.
- Fetching the same data repeatedly. You don't need to fetch multiple times for the same data. You could cache it the first time you fetch it, and reuse it thereafter.

### Is MySQL Examining Too Much Data

Once you are sure your queries retrieve only the data you need, you can look for queries that examine too much data while generating results. In MySQL, the simplest query cost metrics are: 

- Response time. 
- Number of rows examined
- Number of rows returned

None of these metrics is a perfect way to measure query cost, but they reflect roughly how much data access to execute a query and translate approximately into how fast the query runs. All three metrics are logged in the query log, so looking at the query log is one of the best ways to find queries that examine too much data.

**Response time**

Response time is the sum of two things: service time and queue time. Service time is how long it takes the server to actually process the query. Queue time is the portion of response time during which the server isn't really executing the query--it's waiting for something, such as I/O, row lock and so forth. As a result, response time is not consistent under varying load conditions. When you look at a query's response time, you should ask yourself whether the response time is reasonable for the query.

**Rows examined and rows return**

It's useful to think about the number of rows examined when analyzing queries, because you can see how efficiently the queries are finding data you need.

However, not all row accesses are equal. Shorter rows are faster to access, and fetching rows form memory is much faster than reading them from disk.

Ideally, the number of rows examined would be the same as the number returned, but in practice this rarely possible. For example, when constructing rows with joins.

**Rows examined and access types**

MySQL can use several access methods to find and return a row. Some require examining many rows, but others might be without examining any.

The access methods appear in the type column in EXPLAIN's output. The access types range from a full table scan to index scans, range scans, unique index lookups, and constants. Each of these is faster than the one before it, because it requires reading less data.

If you aren't getting a good access type, the best way to solve the problem is usually by adding an appropriate index.

In general, MySQL can apply a WHERE clause in three ways, from best to worst:

- Apply the conditions to the index lookup operation to eliminate nonmatching rows. This happen at the storage engine layer.
- Use a covering index ("Using index" in the Extra column of EXPLAIN) to avoid row accesses and filter out nonmatching rows after retrieving each result from the index.
- Retrieve rows from the table, then filter nonmatching rows ("Using where" in the Extra column of EXPLAIN). This happens at the server layer.

Good indexes help your queries get a good access type and examine only the rows they need. However, adding an index doesn't always mean that MySQL will access fewer rows, or access and return the same number of rows. For example, a query that uses the COUNT() aggregate function.

If you find that a huge number of rows were examined to produce relatively few rows in the result, you can try some more sophisticated fixes:

- Use covering indexes.
- Change the schema.
- Rewrite a complicated query.

## Ways to Restructure Queries

As you optimize problematic queries, your goal should be to find alternative ways to get the result you want. You can sometimes transform queries into equivalent forms that return the same results, and get better performance.

**Complex Queries Versus Many Queries**

It's a good idea to use as few queries as possible, but sometimes you can make a query more efficient by decomposing it and executing a few simple queries instead of one complex one.

**Chopping Up a Query**

Another way to slice up a query is to divide and conquer, keeping it essentially the same but running it in smaller "chunks" that affect fewer rows each time.

Purging old data is a great example. For example:

```sql
DELETE FROM messages WHERE created < DATE_SUB(NOW(), INTERVAL 3 MONTH);
```

It might also be a good idea to add some sleep time between the DELETE statements to spread the load over time and reduce the amount of time locks are held.

**Join Decomposition**

Many high-performance applications use join decomposition. You can decompose a join by running multiple single-table queries instead of a multitable join. For example:

```sql
SELECT * FROM tag
    JOIN tag_post ON tag_post.tag_id=tag.id
    JOIN post ON tag_post.post_d=post.id
WHERE tag.tag='mysql';
```

To

```sql
SELECT * FROM tag WHERE tag='mysql';
SELECT * FROM tag_post WHERE tag_id=1234;
SELECT * FROM post WHERE post.id in (123,456,567,8876);
```

Advantages of multiple single-table queries

- Caching can be more efficient.
- Executing the queries individually can sometimes reduce lock contention.
- The queries themselves can be more efficient.
- You can reduce redundant row accesses.

## Query Execution Basics

If you need to get high performance from your MySQL server, one of the best ways to learning how MySQL optimizes and executes queries. 

The process MySQL to execute queries:

1. The client sends the SQL statement to the server.
2. The server checks the query cache. If there's a hit, it returns the stored result from the cache, otherwise to next step.
3. The server parses, preprocesses, and optimizes the SQL into a query execution plan.
4. The query execution engine executes the plan by making calls to the storage engine API.
5. The server sends the result to the client.

![image-query-process](query-process.png)

**The MySQL Client/Server Protocol**

The protocol is half-duplex, which means that at any given time the MySQL server can be either sending or receiving messages, but not both. This protocol makes MySQL communication simple and fast, but it have some limitation. For example, once one side sends a message, the other side must fetch the entire message before responding.

The client sends a query to the server as a single packet of data. So max_allowed_packet configuration variable is important if you have large queries.

In contrast, the response from the server usually consist of many packets of data. When the server responds, the client has to receive the entire result set. Most libraries that connect to MySQL let you either fetch the whole result and buffer it in memory, or fetch each row as you need it.

Query States

Each MySQL connection, or thread, has a state that shows what it is doing at any given time. There are several ways to view these states, but the easiest is to use the SHOW FULL PROCESSLIST command. The basic states in MySQL:

- Sleep
- Query
- Locked
- Analyzing and statistics
- Copying to tmp table [on disk]
- Sorting result
- Sending data

**The Query Cache**

Before parsing a query, MySQL checks for it in the **query cache**, if the cache is enabled. This operation is a case-sensitive hash lookup. If the query differs from a similar query in the cache by even a single byte, it won't match.

If MySQL does find a match in the query cache, it must check privileges before returning the cache query. If the privileges are OK, MySQL retrieves the stored result from the query cache and sends it to the client. The query is never parsed, optimized, or executed.

**The Query Optimization Process**

After doesn't find a match in the query cache, the next step in the query lifecycle turns a SQL query into an execution plan for the query execute engine. It has several substeps: parsing, preprocessing, and optimization. Errors (like syntax errors) can be raised at any point in the process.

The parser and the preprocessor

- query parsing: MySQL's parser breaks the query into tokens and builds a parse tree from them. The parser uses MySQL's **SQL grammar** to interpret and validate the query.
- query preprocessing: the preprocessor checks the resulting parse tree for **additional semantics**. For example, it checks that tables and columns exist, and it resolves names and aliases to ensure that column references aren't ambiguous. Next, the preprocessor **checks privileges**.

The query optimizer

After preprocessing the parse tree is now valid and ready for the optimizer to turn it into a query execution plan. A query can often be executed many different ways and produce the same result. The optimizer's job is to find the best option.

MySQL uses a cost-based optimizer, which means it tries to predict the cost of various execution plans and choose the least expensive. The unit of cost way originally a single random 4KB data page read, but also includes factors such as the estimated cost of executing a WHERE clause comparison. You can see how expensive the optimizer estimated a query to be by running the query, then inspecting the `Last_query_cost` session variable:

```sql
SELECT SQL_NO_CACHE COUNT(*) FROM your_table;
SHOW STATUS LIKE 'Last_query_cost';
```

Result of `SHOW STATUS LIKE 'Last_query_cost'` means that the optimizer estimated it would need to do about how much random data page reads to execute the query.

The optimizer might not always choose the best plan, for many reasons:

- The statistics could be wrong.
- The cost metric is not exactly equivalent to the true cost of running the query.
- MySQL's idea of optimal might not match yours. MySQL doesn't really try to make queries fast; it tries to minimize their.
- MySQL doesn't consider other queries that are running concurrently.
- MySQL doesn't always do cost-based optimization.
- The optimizer doesn't take into account the cost of operations not under its control, such as executing stored functions.

MySQL's join execution strategy

MySQL consider every query a join--not just every query that matches rows from two tables, but every query. It's very important to understand how MySQL executes joins.

MySQL's join execution strategy is simple: it treats every join as a nested-loop join. This means MySQL runs a loop to find a row from a table, then run a nested loop to find a matching row in the next table. It continues until it has found a matching row in each table in the join. For example:

```sql
SELECT tab1.col1, tab2.col2 
FROM tab1 INNER JOIN tab2 USING(col3)
WHERE tab1.col1 in (5, 6);
```

following pseudocode shows how MySQL might execute the query:

```
outer_iter = iterator over tbl1 where col1 IN(5,6)
outer_row = outer_iter.next
while outer_row
	inner_iter = iterator over tbl2 where col3 = outer_row.col3
	inner_row = inner_iter.next
	while inner_row
		output [ outer_row.col1, inner_row.col2 ]
		inner_row = inner_iter.next
	end
	outer_row = outer_iter.next
end
```

First Finding table iterator that fulfills the WHERE condition, then finding equal values on join columns.

The execution plan

MySQL doesn't generate byte-code to execute a query, instead, the query execution plan is actually a tree of instructions that the query execution engine follows to produce the query results.

Any multitable query can conceptually be represent as a tree. MySQL always begins with one table and finds matching rows in the next table. Thus, MySQL's query execution plans always take the form of left-deep tree. As shown in the figure below.

![Multiple Table Join](multiple-table-join.png)

The join optimizer

The most important part of the MySQL query optimizer is the join optimizer, which decides the best order of execution for multitable queries. 

MySQL's join optimizer can reorder queries to make them less expensive to execute. You can use STRAIGHT_JOIN and write queries in the order you think is best, but such times are rare. In most cases, the join optimizer will outperform a human.

Sort optimizations

...

**The Query Execution Engine**

**Returning Results to the Client**

## Limitations of the MySQL Query Optimizer

...

## Query Optimizer Hints

...

## Optimizing Specific Types of Queries

...

## Conclusion

...

## References