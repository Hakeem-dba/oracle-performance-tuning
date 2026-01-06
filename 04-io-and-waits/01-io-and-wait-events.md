# Section 6 – I/O and Wait Events

## Overview

Oracle Database performance issues are often caused by waiting.  
A wait event indicates that a session is waiting for a resource instead of actively using CPU.

Understanding I/O behavior and wait events is one of the most important skills in performance troubleshooting.  
Wait events explain *why* a session is slow, not just *that* it is slow.

---

## What Are Wait Events?

A wait event represents time spent waiting for a specific resource, such as:
- Data from disk
- CPU availability
- A lock held by another session
- Memory structures

If a session is not on CPU, it is waiting for something.

---

## Main Categories of Wait Events

At a high level, most performance problems fall into three categories:

### CPU-Related Waits
- Sessions are ready to run
- CPU is fully utilized
- Work is queued

### I/O-Related Waits
- Sessions wait for data to be read or written
- Storage latency affects response time

### Lock and Concurrency Waits
- Sessions wait for other sessions
- Blocking and contention occur

This section focuses primarily on **I/O-related waits**.

---

## Why I/O Matters

Disk I/O is significantly slower than memory access.

Typical access times:
- Memory: microseconds
- Disk I/O: milliseconds

Even small increases in storage latency can dramatically increase query execution time when many I/O operations are involved.

---

## Common I/O Wait Events

### db file sequential read

This wait occurs during:
- Single-block reads
- Index lookups
- Row-by-row access patterns

Each wait represents a request for a single data block from disk.

---

#### Performance Implications

High values usually indicate:
- Many index lookups
- Slow storage latency
- Inefficient access paths
- Poorly selective indexes

This wait is common in OLTP systems.

---

#### Typical Causes

- Index access returning many rows
- Indexes with low selectivity
- Storage latency
- Excessive nested loop joins

---

### db file scattered read

This wait occurs during:
- Full table scans
- Full index scans
- Multi-block read operations

Multiple blocks are read in a single I/O operation.

---

#### Performance Implications

High values usually indicate:
- Large full table scans
- Missing or unusable indexes
- Inefficient SQL filtering

This wait is common in reporting and batch workloads.

---

### direct path read

This wait occurs when Oracle reads data directly into PGA memory, bypassing the buffer cache.

Common scenarios:
- Parallel queries
- Large table scans
- Bulk operations

---

#### Performance Implications

- Reduces buffer cache pollution
- Requires sufficient PGA and I/O bandwidth
- Can stress storage during large operations

---

### direct path write

This wait occurs during:
- Bulk inserts
- Parallel DML
- Large sorting operations

Data is written directly to disk, bypassing the buffer cache.

---

### TEMP-Related I/O Waits

#### direct path read temp
#### direct path write temp

These waits occur when:
- PGA memory is insufficient
- Sorting or hashing spills to TEMP

High TEMP I/O waits usually indicate:
- Low PGA allocation
- Inefficient SQL
- Large unfiltered result sets

---

## Interpreting I/O Waits Correctly

High I/O waits do not always mean storage is slow.

They can also indicate:
- SQL reading too much data
- Poor indexing
- Inefficient execution plans

Always analyze:
- Execution plans
- SQL access paths
- Data volume processed

before concluding that storage is the problem.

---

## Storage Latency and Its Impact

Latency is the time required to return a block from storage.

Example:
- 40,000 reads × 10 ms latency = 400 seconds
- 40,000 reads × 0.5 ms latency = 20 seconds

The same SQL can behave very differently depending on storage performance.

---

## Identifying I/O Bottlenecks

Common signs of I/O-related issues:
- Low CPU usage but slow response time
- High I/O wait events
- Queries slow only when accessing data
- TEMP usage growing during execution

I/O bottlenecks often coexist with inefficient SQL.

---

## Relationship Between I/O and SQL Design

SQL design has a direct impact on I/O behavior.

Examples:
- Missing indexes cause full table scans
- Poor join conditions increase read volume
- Selecting unnecessary columns increases I/O
- Unfiltered queries increase data access

Reducing data access is often more effective than increasing hardware capacity.

---

## Using Wait Events in Troubleshooting

A simple diagnostic approach:
1. Identify top wait events
2. Determine the category (CPU, I/O, lock)
3. Identify the SQL causing the waits
4. Analyze execution plans
5. Apply targeted fixes
6. Validate improvement

Wait events provide direction, not final answers.

---

## Common Mistakes When Analyzing Waits

- Looking at wait events without SQL context
- Assuming storage is slow without evidence
- Ignoring execution plans
- Treating symptoms instead of root causes
- Focusing on a single metric in isolation

Effective analysis requires correlation between waits, SQL, and workload.

---

## Summary

- Wait events explain where time is spent
- I/O waits are a common source of slow performance
- db file sequential read indicates single-block reads
- db file scattered read indicates full scans
- direct path and TEMP waits indicate large operations or memory pressure
- SQL design directly affects I/O behavior

Understanding I/O and wait events is essential for diagnosing and resolving performance issues.

---

## Next Section

**Section 7 – Concurrency, Locks, and Blocking**

The next section explains how concurrent access, locks, and blocking affect performance and system responsiveness.
