# Section 3 – SGA Deep Dive

## Overview

The System Global Area (SGA) is a shared memory region used by all sessions connected to an Oracle database.  
Its primary purpose is to reduce disk I/O, minimize parsing overhead, and improve overall performance by caching frequently used data and SQL structures.

A well-sized and well-managed SGA is critical for stable and predictable database performance.

---

## What Is the SGA?

The SGA is allocated at instance startup and shared across all user sessions.

It contains:
- Data blocks read from disk
- SQL statements and execution plans
- Metadata required for SQL execution
- Memory structures used by background processes

Because the SGA is shared, inefficient usage by one workload can affect all other sessions.

---

## Main SGA Components

### Buffer Cache

#### Purpose
The buffer cache stores copies of data blocks read from datafiles.

When a session needs a block:
- If the block exists in the buffer cache → memory access (fast)
- If not → physical read from disk (slow)

The buffer cache is the most important component for read performance.

---

#### Performance Impact

A healthy buffer cache:
- Reduces physical reads
- Improves response time
- Reduces storage load

Common buffer cache problems:
- Repeated physical reads for the same data
- Large full table scans pushing useful blocks out
- Contention on hot blocks

---

#### Typical Symptoms of Buffer Cache Issues

- Queries do not get faster on repeated execution
- High physical read counts
- High wait events such as:
  - `buffer busy waits`
  - `free buffer waits`
- High I/O load despite sufficient memory

---

#### Common Causes

- Buffer cache too small
- Large full table scans on big tables
- Poor indexing
- OLTP and reporting workloads competing for cache

---

#### General Fixes

- Increase buffer cache size (after analysis)
- Add or improve indexes to reduce full scans
- Separate reporting workloads from OLTP where possible
- Schedule heavy scans during off-peak hours

---

## Shared Pool

### Purpose

The shared pool stores:
- Parsed SQL statements
- Execution plans
- Data dictionary information
- PL/SQL code

Its main goal is to reduce parsing overhead and CPU usage by reusing SQL structures.

---

### Parsing Explained

Parsing is the process Oracle uses to prepare SQL for execution.

Parsing involves:
- Syntax validation
- Object validation
- Privilege checking
- Execution plan generation
- Memory allocation

---

#### Hard Parse vs Soft Parse

**Hard Parse**
- SQL is not found in the shared pool
- Oracle must fully parse and optimize the statement
- CPU intensive and slow

**Soft Parse**
- SQL already exists in the shared pool
- Oracle reuses existing execution structures
- Much faster and more efficient

---

### Performance Impact of Poor Shared Pool Usage

Symptoms include:
- High CPU usage
- Excessive hard parsing
- Many similar SQL statements with different literals
- Unstable performance during peak times
- ORA-4031 errors in extreme cases

---

### Common Causes

- Applications not using bind variables
- Very small shared pool size
- Excessive dynamic SQL
- Frequent invalidations

---

### General Fixes

- Use bind variables
- Keep SQL text consistent
- Size shared pool appropriately
- Avoid unnecessary dynamic SQL

---

## Large Pool

### Purpose

The large pool is used for:
- RMAN backup operations
- Parallel execution
- Shared server session memory

It prevents large memory allocations from impacting the shared pool.

---

### Performance Considerations

If the large pool is undersized:
- RMAN operations can steal memory from the shared pool
- SQL performance may degrade during backups
- Parsing efficiency may decrease

If oversized:
- Memory may be wasted that could benefit other SGA components

---

## SGA Memory Contention

Because the SGA is shared:
- One inefficient workload can affect all sessions
- Poor memory balance leads to contention
- Sudden workload changes can destabilize performance

Common signs of SGA pressure:
- Increased wait events
- Sudden CPU spikes
- Increased physical I/O
- Parsing-related waits

---

## Observing SGA Behavior

Common areas to monitor:
- Physical vs logical reads
- Hard parse rates
- Shared pool usage
- Buffer cache hit ratio (used cautiously)
- SGA-related wait events

Metrics should always be interpreted in context and workload patterns.

---

## Why SGA Tuning Requires Caution

Blindly increasing SGA components can:
- Starve PGA memory
- Increase OS memory pressure
- Cause swapping
- Hide underlying SQL problems

SGA tuning should always be based on evidence, not assumptions.

---

## Summary

- The SGA is shared memory used by all sessions
- Buffer cache affects read performance
- Shared pool affects parsing and CPU usage
- Large pool protects shared pool during heavy operations
- Poor SGA usage affects the entire database

Understanding SGA behavior is essential before tuning SQL, memory, or storage.

---

## Next Section

**Section 4 – PGA and SQL Execution**

The next section explains how PGA memory is used during sorting, joins, and SQL execution, and how insufficient PGA leads to TEMP usage and slow performance.
