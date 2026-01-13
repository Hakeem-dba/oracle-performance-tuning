# Section 11.3 – Sorting, Hashing, and TEMP Usage

## Overview

Sorting and hashing operations are common in SQL execution.
They are used for operations such as ORDER BY, GROUP BY, DISTINCT, joins, and analytic functions.

When these operations cannot be completed in memory, Oracle uses the TEMP tablespace.
Excessive TEMP usage is often a symptom of SQL design or memory pressure rather than a storage problem.

---

## Sorting Operations

### When Sorting Occurs

Sorting is required for operations such as:
- ORDER BY
- GROUP BY
- DISTINCT
- MERGE JOIN
- analytic functions (OVER, PARTITION BY)

If the sort cannot be completed in memory, Oracle spills data to TEMP.

---

### Memory vs TEMP Sorting

- in-memory sort: faster, preferred
- disk-based sort (TEMP): slower, increases I/O and response time

Whether a sort uses memory or TEMP depends on:
- PGA availability
- size of the result set
- degree of parallelism

---

## Hashing Operations

### Hash Join and Hash Aggregation

Hashing is commonly used for:
- HASH JOIN
- hash-based GROUP BY

Oracle builds a hash table in memory.
If the hash table does not fit in memory, Oracle spills partitions to TEMP.

---

### TEMP Usage in Hash Operations

Hash operations may:
- partially spill to TEMP
- fully spill to TEMP if memory is insufficient

Heavy TEMP usage during hash joins is a strong indicator of:
- large data sets
- insufficient PGA
- inefficient filtering

---

## Common TEMP-Related Wait Events

Typical wait events include:
- direct path write temp
- direct path read temp

These events indicate disk-based processing of intermediate results.

High values usually point to memory pressure or inefficient SQL.

---

## Causes of Excessive TEMP Usage

### Large Result Sets

Operations processing many rows often require:
- large sorts
- large hash tables

Filtering data earlier in the execution plan reduces TEMP usage significantly.

---

### Inefficient SQL Design

Common design issues:
- missing WHERE clause filters
- unnecessary ORDER BY or DISTINCT
- selecting more columns than required
- joining tables before filtering

SQL design has a greater impact on TEMP usage than memory sizing alone.

---

### Excessive Parallel Execution

Parallel execution multiplies memory usage.
Each parallel process requires its own work area.

High parallelism can exhaust PGA quickly and force TEMP spills.

---

## Identifying TEMP Usage

### Identify Sessions Using TEMP

```sql
SELECT sid,
       serial#,
       tablespace,
       blocks * 8192 / 1024 / 1024 AS temp_mb
FROM v$tempseg_usage
ORDER BY temp_mb DESC;
```

```sql

```

```sql
SELECT sid,
       serial#,
       sql_id
FROM v$session
WHERE sid = <SID>;
```

Then retrieve the SQL text:
```sql
SELECT sql_id,
       sql_text
FROM v$sql
WHERE sql_id = '<SQL_ID>';
```
This helps determine why the SQL requires sorting or hashing.

##How to Reduce TEMP Usage
###SQL-Level Improvements
- reduce data volume early using filters
- remove unnecessary ORDER BY and DISTINCT
- review join order and join methods
- select only required columns

SQL tuning is usually the most effective solution.

###Memory Considerations
- review PGA_AGGREGATE_TARGET
- monitor PGA usage during peak load
- avoid increasing memory without evidence
Memory changes should follow SQL analysis, not precede it.

###Control Parallel Execution

- limit degree of parallelism
- avoid parallel execution for OLTP queries
- schedule parallel workloads during off-peak hours
Parallelism improves performance only when resources are sufficient.

##Common Mistakes

Avoid the following mistakes:
- increasing TEMP size instead of fixing SQL
- assuming storage performance is the root cause
- ignoring execution plans
- blindly increasing PGA settings
TEMP is usually a symptom, not the root cause.

##Summary
- sorting and hashing are common SQL operations
- insufficient memory causes spills to TEMP
- TEMP usage increases I/O and response time
- SQL design strongly influences TEMP usage
- effective tuning starts with execution plan analysis
