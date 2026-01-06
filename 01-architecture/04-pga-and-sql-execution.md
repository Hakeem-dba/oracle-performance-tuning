# Section 4 – PGA and SQL Execution

## Overview

The Program Global Area (PGA) is private memory allocated to each Oracle session.  
It is primarily used during SQL execution for operations that require working memory, such as sorting, hashing, and joining data.

Unlike the SGA, the PGA is **not shared**. Each session has its own PGA memory, which makes PGA sizing critical in systems with many concurrent sessions.

---

## What Is the PGA?

The PGA is created when a session starts and released when the session ends.

It is used for:
- Sorting operations (ORDER BY)
- Aggregation (GROUP BY, DISTINCT)
- Hash joins
- Bitmap operations
- Window functions
- PL/SQL execution memory
- Session variables and runtime data

The PGA can be thought of as the working area for SQL execution.

---

## Why PGA Matters for Performance

Memory access is significantly faster than disk access.

When SQL operations can complete entirely in PGA memory:
- Execution is fast
- No disk I/O is required
- TEMP tablespace is not used

When PGA is insufficient:
- Oracle spills data to the TEMP tablespace
- Disk I/O increases dramatically
- Query response time degrades

---

## Sorting Operations

Sorting occurs in many common SQL operations, including:
- ORDER BY
- GROUP BY
- DISTINCT
- Window functions

### In-Memory Sort
If sufficient PGA is available:
- Sorting happens entirely in memory
- Execution is fast

### Disk-Based Sort
If PGA is insufficient:
- Sorting spills to TEMP
- Disk I/O is introduced
- Performance degrades significantly

High TEMP usage is often an indicator of PGA pressure.

---

## Hash Joins

Hash joins are commonly used when joining large tables.

### How Hash Joins Work
1. Oracle reads the smaller input set
2. Builds a hash table in PGA memory
3. Reads the larger input set
4. Probes the hash table to find matching rows

Hash joins require sufficient PGA to store the hash table.

---

### Hash Join Spills

If PGA is too small:
- Hash tables cannot fit in memory
- Data spills to TEMP
- Execution time increases significantly

Hash join spills are a common cause of poor query performance in reporting and ETL workloads.

---

## Window Functions

Functions such as:
- ROW_NUMBER
- RANK
- LAG
- LEAD
- Analytic aggregations

often require sorting within partitions.

These operations are PGA-intensive and frequently cause TEMP usage when memory is insufficient.

---

## TEMP Tablespace Usage

The TEMP tablespace is used as an extension of PGA when memory is insufficient.

Common operations that use TEMP:
- Large sorts
- Hash joins on large data sets
- Parallel execution
- Complex aggregations

Frequent or excessive TEMP usage is usually a sign of:
- Low PGA allocation
- Inefficient SQL
- Large unfiltered data sets

---

## Identifying PGA-Related Problems

Common symptoms include:
- Queries slow during sorting or joining
- TEMP tablespace growing rapidly
- Wait events related to TEMP I/O
- Reports and batch jobs taking much longer than expected

These symptoms often appear even when CPU usage is low.

---

## PGA Management Concepts

Oracle manages PGA memory using:
- PGA_AGGREGATE_TARGET
- Work area memory policies

PGA memory is shared across sessions at the instance level, but allocated privately per session at runtime.

High concurrency increases total PGA demand.

---

## Common Causes of PGA Pressure

- Many concurrent sessions performing sorts or joins
- Large result sets without filtering
- Inefficient SQL generating unnecessary data
- Excessive parallel execution
- Underestimated PGA requirements

---

## General Approaches to Improvement

- Increase PGA allocation after analysis
- Reduce data volume processed by SQL
- Filter rows early in queries
- Add indexes to reduce sorting and hashing
- Reduce parallelism where appropriate

Increasing PGA should not be the first action without understanding workload behavior.

---

## Relationship Between PGA and SQL Execution Plans

Execution plans reveal:
- Join methods used
- Sort operations
- Hash operations
- Potential TEMP usage

Hash joins and sort operations in execution plans indicate higher PGA demand.

Understanding execution plans helps predict memory behavior before problems occur.

---

## Summary

- PGA is private memory used by each session
- Sorting and hash joins are PGA-intensive operations
- Insufficient PGA leads to TEMP spills and slow performance
- TEMP usage is a strong indicator of PGA pressure
- SQL design directly affects PGA consumption

Understanding how SQL uses PGA is essential for diagnosing performance issues related to memory and execution behavior.

---

## Next Section

**Section 5 – Background Processes and Performance Impact**

The next section explains key Oracle background processes and how their behavior affects performance and stability.
