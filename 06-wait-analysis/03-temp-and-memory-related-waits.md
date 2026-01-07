# Section 8.3 – TEMP and Memory-Related Waits

## Overview

TEMP and memory-related waits occur when SQL operations cannot complete using available memory.
When this happens, Oracle spills work to disk, most commonly to the TEMP tablespace.

These waits are a strong indicator of:
- insufficient PGA memory
- inefficient SQL processing large data sets
- inappropriate parallel execution

Understanding TEMP and memory waits helps distinguish between SQL design issues and memory sizing problems.

---

## Why TEMP Is Used

Oracle uses the Program Global Area (PGA) for in-memory operations such as:
- sorting
- hash joins
- aggregations
- analytic functions

When PGA memory is insufficient, Oracle uses the TEMP tablespace as overflow space.
Disk-based operations are significantly slower than in-memory operations.

---

## Common TEMP and Memory-Related Wait Events

### direct path read temp

This wait occurs when Oracle reads data from the TEMP tablespace during:
- disk-based sorts
- hash join spills
- large aggregations

High values usually indicate that SQL operations are spilling to TEMP.

---

### direct path write temp

This wait occurs when Oracle writes intermediate results to the TEMP tablespace.

Frequent write waits indicate:
- insufficient PGA
- large unfiltered result sets
- inefficient execution plans

---

### PGA Memory-Related Symptoms (Without TEMP Waits)

Not all memory issues appear as TEMP waits.
Some workloads experience:
- frequent work area resizing
- reduced in-memory operations
- unstable performance under load

These issues may still be related to PGA pressure even if TEMP waits are moderate.

---

## Common Causes of TEMP and Memory Waits

### Large Sort Operations

Operations such as:
- ORDER BY
- GROUP BY
- DISTINCT
- analytic functions

can require large amounts of memory.
When result sets are large, TEMP usage increases.

---

### Hash Joins on Large Data Sets

Hash joins build in-memory hash tables.
If the hash table does not fit in PGA memory, Oracle spills to TEMP.

This significantly increases execution time.

---

### Excessive Parallel Execution

Parallel execution multiplies memory usage.
Each parallel process requires its own PGA memory.

High parallelism can exhaust available PGA quickly and force TEMP usage.

---

### Inefficient SQL Design

Examples include:
- missing filters
- selecting unnecessary columns
- processing more data than required
- poor join conditions

Reducing data volume often reduces TEMP usage more effectively than increasing memory.

---

## Identifying TEMP-Related Problems

### Detect TEMP Usage by Session

```sql
SELECT sid,
       serial#,
       username,
       tablespace,
       blocks * 8192 / 1024 / 1024 AS temp_mb
FROM v$tempseg_usage
ORDER BY temp_mb DESC;
```
This query identifies sessions consuming TEMP space.

## Correlate TEMP Usage with SQL
Once the session is identified, retrieve the SQL ID:
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

This allows you to analyze why the SQL requires TEMP.

## How to Reduce TEMP and Memory Waits

Reducing TEMP and memory-related waits requires identifying whether the issue is caused by SQL design, memory configuration, or workload behavior.

In most cases, SQL optimization provides the largest and safest improvement.

---

### SQL-Level Improvements

Focus first on reducing the amount of data processed by SQL statements.

Common actions include:
- filtering rows as early as possible
- avoiding unnecessary ORDER BY, GROUP BY, and DISTINCT operations
- reviewing join conditions to prevent large intermediate result sets
- selecting only required columns instead of using SELECT *

Optimized SQL often eliminates TEMP usage without changing memory settings.

---

### Memory Configuration Review

If SQL is efficient but TEMP usage remains high:
- review PGA_AGGREGATE_TARGET sizing
- monitor PGA usage during peak workload
- ensure the operating system is not under memory pressure

Memory changes should be based on observed workload behavior, not assumptions.

---

### Parallel Execution Control

Parallel execution increases memory usage because each parallel process requires its own PGA.

To reduce TEMP pressure:
- lower the degree of parallelism
- avoid parallel execution for OLTP queries
- schedule parallel workloads during off-peak hours

Parallel execution improves performance only when sufficient CPU and memory resources are available.

---

## Common Mistakes

Avoid the following common mistakes:
- increasing TEMP tablespace size instead of fixing SQL
- increasing PGA blindly without understanding workload patterns
- assuming storage performance is the root cause
- ignoring execution plans and data volume

TEMP usage is usually a symptom, not the root problem.

---

## Summary

- TEMP and memory waits occur when operations spill from memory to disk
- SQL design has the biggest impact on TEMP usage
- memory configuration should be adjusted only after SQL review
- excessive parallel execution increases TEMP pressure
- effective resolution starts with analysis, not quick fixes
