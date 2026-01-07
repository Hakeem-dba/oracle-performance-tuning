# Section 9.4 – Top SQL Analysis

## Overview

In most Oracle databases, a **small number of SQL statements consume the majority of resources**.
Top SQL Analysis focuses on identifying these statements and understanding *why* they are expensive.

This section explains how to analyze top SQL in an AWR report and how to decide the correct next action.

---

## Why Top SQL Matters

Performance problems are rarely caused by the database as a whole.
They are usually caused by:
- inefficient SQL
- SQL accessing too much data
- SQL executed too frequently
- SQL affected by poor execution plans

Identifying and fixing a few high-impact SQL statements often delivers the largest performance improvement.

---

## Where to Find Top SQL in AWR

An AWR report includes multiple SQL sections, typically ordered by:
- elapsed time
- CPU time
- logical reads
- physical reads
- executions

Each view highlights a different type of inefficiency.

---

## SQL Ordered by Elapsed Time

This section shows SQL statements that consumed the most total execution time.

Focus on:
- SQL with high elapsed time and low execution count
- SQL with high average elapsed time per execution

These statements usually affect user response time directly.

---

## SQL Ordered by CPU Time

This section highlights SQL that consumes the most CPU.

High CPU SQL often indicates:
- inefficient execution plans
- excessive parsing
- complex joins or calculations
- missing or unusable indexes

CPU-heavy SQL is a common cause of CPU-bound systems.

---

## SQL Ordered by Logical Reads

Logical reads represent how many blocks are accessed from memory.

High logical reads usually indicate:
- inefficient access paths
- full table scans on large tables
- poor join methods

Reducing logical reads often improves both CPU and I/O performance.

---

## SQL Ordered by Physical Reads

Physical reads show how much data is read from disk.

High physical reads often point to:
- missing indexes
- large data scans
- ineffective caching
- I/O-bound workloads

Always correlate physical reads with execution plans before blaming storage.

---

## SQL Ordered by Executions

SQL with very high execution counts may:
- be lightweight but called excessively
- indicate chatty application behavior
- cause cumulative CPU or parse overhead

Sometimes reducing execution frequency is more effective than tuning the SQL itself.

---

## Interpreting SQL Statistics Together

Never analyze a single metric in isolation.

Useful comparisons include:
- elapsed time vs CPU time
- executions vs total time
- logical reads vs physical reads

For example:
- high elapsed time with low CPU indicates waiting
- high CPU with low elapsed time indicates heavy processing
- high reads per execution indicates inefficient access

---

## Identifying High-Impact SQL

Prioritize SQL that:
- appears across multiple AWR sections
- has high per-execution cost
- directly affects business-critical operations
- consumes a large percentage of DB time

These SQL statements offer the best tuning return.

---

## Next Steps After Identifying Top SQL

Once a problematic SQL statement is identified:
1. capture the SQL text
2. review the execution plan
3. check object statistics
4. validate index usage
5. test improvements in a controlled environment

AWR identifies *what* is expensive; execution plans explain *why*.

---

## Common Mistakes in Top SQL Analysis

Avoid these mistakes:
- tuning SQL that is not business-critical
- focusing only on total executions
- ignoring per-execution cost
- changing indexes without plan analysis
- assuming high resource usage always means a problem

Context is essential.

---

## Summary

- a small number of SQL statements usually dominate resource usage
- AWR provides multiple views of top SQL behavior
- elapsed time, CPU, and reads highlight different inefficiencies
- effective tuning starts with correct SQL prioritization
- execution plan analysis is the next step after AWR

Top SQL Analysis bridges AWR diagnostics and SQL tuning.
