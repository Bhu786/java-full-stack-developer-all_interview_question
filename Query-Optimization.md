# 1. suppose a query that used to run in 1 second is now taking 30 secs. how would you investigate it?


Yes — your understanding is **correct**. For a 3–5 year experience interview, I would make it a little more structured and add a few important optimization techniques so you can handle **scenario-based questions**, not just definitions.

## 1. What is SQL Query Optimization?

A good interview definition:

> **SQL optimization is the process of improving query performance by reducing execution time, unnecessary CPU/memory usage, disk I/O, and the amount of data processed, while still getting the correct result.**

In simple words:

**Slow query → find the bottleneck → optimize query/index/database design → verify performance.**

---

# 2. Important SQL Optimization Techniques

### 1. Avoid N+1 Query Problem

Suppose we have an API:

`GET /orders`

First query:

```sql
SELECT * FROM orders;
```

Suppose it returns 100 orders.

Then application executes another query for each order:

```sql
SELECT * FROM order_items WHERE order_id = 1;
SELECT * FROM order_items WHERE order_id = 2;
SELECT * FROM order_items WHERE order_id = 3;
...
```

So:

**1 query + 100 queries = 101 queries**

That's the **N+1 problem**.

### Solution

Use a JOIN when appropriate:

```sql
SELECT o.id, o.order_date, oi.product_id, oi.quantity
FROM orders o
JOIN order_items oi
ON o.id = oi.order_id;
```

In ORM/JPA, depending on the use case, you can use:

* `JOIN FETCH`
* Entity graphs
* Batch fetching
* Appropriate eager/lazy loading

**Interview point:** Don't blindly say "use EAGER." Eager loading can itself cause performance problems if too much data is loaded. Prefer fetching only the data actually required.

---

# 3. Pagination

If a table contains millions of records, don't return everything.

Bad:

```sql
SELECT * FROM orders;
```

Better:

```sql
SELECT id, order_date, total_amount
FROM orders
LIMIT 20 OFFSET 0;
```

For the next page:

```sql
LIMIT 20 OFFSET 20;
```

For very large datasets, **keyset/cursor pagination** can perform better than large offsets.

Example:

```sql
SELECT id, order_date
FROM orders
WHERE id > 1000
ORDER BY id
LIMIT 20;
```

### Interview scenario

**Interviewer:** Your API returns 10 lakh records and is slow. What will you do?

**Answer:**

> "I would first check whether the API really needs to return all records. If not, I would introduce pagination and return only the required records. For very large datasets, I would also consider keyset pagination."

---

# 4. Avoid `SELECT *`

Instead of:

```sql
SELECT *
FROM employees
WHERE department_id = 10;
```

Use:

```sql
SELECT id, name, salary
FROM employees
WHERE department_id = 10;
```

Why?

Because `SELECT *` may:

* Fetch unnecessary columns
* Increase network transfer
* Increase memory usage
* Increase disk I/O
* Make queries less maintainable

---

# 5. Indexing

Indexes help the database find rows faster instead of scanning the entire table.

Example:

```sql
SELECT *
FROM employees
WHERE email = 'abc@gmail.com';
```

If `email` is frequently used for searching, an index can help:

```sql
CREATE INDEX idx_employee_email
ON employees(email);
```

Without a useful index, the database may perform a **full table scan**.

### But important interview point:

Don't say:

> "More indexes always improve performance."

That's incorrect.

Indexes also have costs:

* Extra storage
* INSERT becomes more expensive
* UPDATE can become more expensive
* DELETE can become more expensive

So:

> **Create indexes based on actual query patterns and verify their usefulness using the execution plan.**

---

# 6. EXPLAIN

This is one of the most important things you mentioned.

### Definition

> **EXPLAIN shows the database's execution plan for a SQL query, helping us understand how the database intends to execute that query.**

For example:

```sql
EXPLAIN
SELECT *
FROM employees
WHERE department_id = 10;
```

Depending on the database, you may use:

```sql
EXPLAIN ANALYZE
```

`EXPLAIN ANALYZE` can actually execute the query and provide runtime information in databases that support it.

---

# 7. What do we check in EXPLAIN?

You can say:

> "I use EXPLAIN to analyze the execution plan and check whether indexes are being used, whether a full table scan is happening, how many rows are being examined, how joins are executed, and whether expensive sorting or other operations are occurring."

Important things to look for:

### 1. Full table scan

If a huge table is being scanned unnecessarily, investigate indexing/filtering.

### 2. Index usage

Check whether the expected index is being used.

### 3. Rows examined

If the query examines millions of rows to return 10 rows, that's a potential optimization area.

### 4. Join strategy

Check how tables are being joined and whether join columns are appropriately indexed.

### 5. Sorting

Large `ORDER BY` operations can become expensive.

### 6. Filtering

Check whether filtering is happening efficiently.

---

# 8. Production Scenario — API is Slow

This is **very important for your interview**.

### Interviewer:

> "One of your production APIs has suddenly become slow. How will you troubleshoot it?"

Don't immediately say:

> "I'll add an index."

Instead give a systematic approach.

### Your answer:

> "First, I would identify whether the slowness is coming from the application, database, network, or an external dependency. If database latency is the suspected bottleneck, I would check the query execution time and database monitoring metrics. Then I would analyze the slow query using EXPLAIN or EXPLAIN ANALYZE to check the execution plan, index usage, rows scanned, full table scans, joins, and expensive sorting operations. Based on the bottleneck, I would optimize the query, add or modify an appropriate index, reduce unnecessary columns, introduce pagination, or optimize joins. After the change, I would test the query again and compare execution time and database resource usage before deploying the change."

This sounds much more like **real production experience**.

---

# 9. Scenario: Query Takes 10 Seconds

**Interviewer:**

> "A query takes 10 seconds. What will you do?"

Answer:

> "First I would reproduce and measure the query execution time. Then I would run EXPLAIN or EXPLAIN ANALYZE and check whether there is a full table scan, inefficient index usage, excessive rows being scanned, expensive joins, or sorting. Based on the execution plan, I would optimize the query or add an appropriate index. Then I would run the query again and compare the execution time."

---

# 10. Scenario: Index Exists but Query Is Still Slow

This is a **very good follow-up question**.

**Interviewer:**

> "There is already an index. Why is the query still slow?"

Possible reasons:

* Database may not choose that index
* Low-selectivity column
* Query returns a large percentage of the table
* Function applied to indexed column
* Type conversion
* Leading wildcard in `LIKE`
* Composite index order isn't appropriate
* Outdated statistics
* Expensive joins/sorts
* Too much data being returned

Example:

```sql
WHERE LOWER(email) = 'abc@gmail.com'
```

Depending on the database, a normal index on `email` may not be usable efficiently because a function is applied to the column.

---

# 11. Scenario: `LIKE` Query Is Slow

Suppose:

```sql
SELECT *
FROM employees
WHERE name LIKE '%kumar%';
```

The leading `%` can make a normal B-tree index ineffective for this type of search.

You would investigate:

* Search requirements
* Full-text search
* Appropriate database-specific indexing
* Search engine if the use case requires it

Don't simply say "add an index."

---

# 12. Scenario: JOIN Is Slow

Suppose:

```sql
SELECT *
FROM orders o
JOIN customers c
ON o.customer_id = c.id;
```

You would check:

* Join columns
* Appropriate indexes
* Number of rows involved
* Execution plan
* Whether unnecessary rows/columns are being processed

For example, `orders.customer_id` may need an index depending on the workload and query pattern.

---

# 13. Scenario: API Is Returning Too Much Data

Interviewer:

> "The API is slow because the database is returning huge data. What will you do?"

Answer:

> "I would avoid fetching unnecessary columns, avoid SELECT *, introduce pagination, and return only the data required by the API. If the query is still slow, I would analyze the execution plan and check indexing and filtering."

---

# 14. Scenario: Query Works Fine in Development but Slow in Production

This is another excellent question.

Possible reasons:

* Production has much more data
* Different indexes
* Different database configuration
* Outdated statistics
* Different execution plan
* Higher concurrent traffic
* Resource contention
* Locking/blocking
* Network latency

Good answer:

> "I would compare the production and development environments, especially data volume, indexes, execution plans, database resources, and concurrency. I would analyze the production query using EXPLAIN/EXPLAIN ANALYZE and check database monitoring rather than assuming the query itself is the only problem."

---

# 15. Scenario: Adding Index Made Performance Worse

Interviewer:

> "You added an index, but performance became worse. Why?"

Answer:

> "Indexes are not free. They consume storage and add overhead to INSERT, UPDATE and DELETE operations. Also, an index may not help if the column has low selectivity or if the query doesn't use it efficiently. I would verify the execution plan and workload before keeping the index."

---

# 16. Scenario: How Do You Know Your Optimization Worked?

Very important.

Don't just say:

> "Query became faster."

Say:

> "I compare the execution time and execution plan before and after the change. I also check rows examined, index usage, CPU, I/O and database resource utilization where applicable. Then I validate the change under realistic data volume and load."

---

# 17. Your Complete Interview Answer

If interviewer simply asks:

### **"What SQL optimization techniques have you used?"**

You can answer:

> "SQL optimization is the process of improving query performance by reducing execution time, unnecessary CPU, memory, disk I/O and data processing. In my projects, some common techniques I consider are avoiding N+1 queries, using pagination for large datasets, selecting only required columns instead of SELECT *, and creating appropriate indexes based on query patterns. I also use EXPLAIN or EXPLAIN ANALYZE to understand the execution plan and identify issues such as full table scans, inefficient index usage, excessive rows being scanned, expensive joins or sorting. In a production scenario, if an API becomes slow, I first identify whether the database is the bottleneck, measure the query performance, analyze the execution plan, optimize the query or index based on the actual bottleneck, and then compare the performance before and after the change."

That's a **strong 3–5 year experience-level answer**.

### Your optimization flow to remember

**Slow API**

↓

**Check application / DB / external dependency**

↓

**Find slow query**

↓

**Measure execution time**

↓

**EXPLAIN / EXPLAIN ANALYZE**

↓

**Check**

* Full table scan?
* Index used?
* Too many rows scanned?
* Expensive JOIN?
* Expensive SORT?
* Unnecessary columns?
* N+1?
* Large result set?

↓

**Optimize**

* Proper index
* Better JOIN
* Pagination
* Required columns only
* Remove N+1
* Query rewrite

↓

**Test again**

↓

**Compare before vs after**

This is the flow you should memorize rather than memorizing 15 isolated definitions.
