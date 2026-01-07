# Section 9.2 – Understanding an AWR Report

## Overview

An AWR report compares database performance between two snapshots.
Its purpose is not to provide direct fixes, but to **guide investigation** by highlighting where time and resources were spent.

Correct interpretation of the report is more important than generating it.

---

## Before Reading an AWR Report

Always confirm the following before analysis:
- snapshots cover the time of the reported issue
- workload during the snapshot period is representative
- comparison is between similar workload periods
- the issue is not caused by external factors (application outage, batch job, maintenance)

Analyzing the wrong snapshot range leads to incorrect conclusions.

---

## Load Profile

The Load Profile section summarizes database activity per second.

Key metrics include:
- DB Time
- DB CPU
- Logical reads
- Physical reads
- Executions
- Transactions

DB Time represents the total time spent by sessions inside the database.
If DB Time is much higher than DB CPU, sessions are spending significant time waiting.

---

## Instance Efficiency Indicators

This section provides high-level ratios related to:
- buffer cache efficiency
- parsing behavior
- redo generation

These values should be used cautiously.
They are indicators, not tuning targets.

Do not attempt to tune the database to achieve “perfect” percentages.

---

## Top Timed Events

The Top Timed Events section shows where most DB time was spent.

Focus on:
- total time
- percentage of DB time
- wait class

This section helps determine whether the system is:
- CPU-bound
- I/O-bound
- lock-bound
- commit-bound

Idle wait events should be ignored.

---

## Wait Classes

Wait Classes group related wait events and provide fast bottleneck classification.

Key observations:
- User I/O dominance usually indicates heavy data access
- Concurrency dominance indicates locking or contention
- Commit dominance points to redo-related issues
- CPU dominance suggests inefficient SQL or insufficient CPU

Use wait classes to decide where to drill down next.

---

## SQL Ordered by Resource Usage

This section lists SQL statements ordered by:
- elapsed time
- CPU time
- logical reads
- physical reads
- executions

This is one of the most valuable sections in the report.

A small number of SQL statements often consume the majority of resources.

---

## Interpreting SQL Statistics

When reviewing top SQL:
- compare elapsed time to CPU time
- check executions versus total time
- identify SQL with high per-execution cost
- look for SQL that appears unexpectedly

High elapsed time with low CPU usually indicates waiting.
High CPU with low elapsed time indicates efficient but heavy CPU usage.

---

## I/O Statistics

I/O sections show:
- read and write activity
- I/O latency
- distribution between datafiles and redo logs

High I/O activity should be correlated with SQL access patterns before assuming storage issues.

---

## Segment Statistics

Segment statistics identify objects with high:
- logical reads
- physical reads
- buffer busy waits

This helps connect SQL behavior to specific tables or indexes.

---

## Common Misinterpretations

Avoid these common mistakes:
- focusing on a single section only
- tuning based solely on ratios
- ignoring workload context
- assuming the top wait event is always the root cause
- changing parameters without SQL analysis

An AWR report provides direction, not final answers.

---

## Recommended Analysis Flow

A practical approach:
1. Verify snapshot range
2. Review Load Profile
3. Check Top Timed Events and Wait Classes
4. Identify top SQL by resource usage
5. Correlate SQL with waits and I/O
6. Validate findings using V$ views or ASH

This keeps analysis structured and efficient.

---

## Summary

- an AWR report compares performance between two snapshots
- correct snapshot selection is critical
- Top Timed Events and Wait Classes identify bottlenecks
- SQL sections reveal the main consumers of resources
- interpretation and context matter more than raw numbers

Understanding how to read an AWR report is essential for effective performance troubleshooting.
