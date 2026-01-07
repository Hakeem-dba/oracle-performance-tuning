# Section 9.3 – Top Timed Events and Wait Classes

## Overview

The Top Timed Events and Wait Classes sections are the most important parts of an AWR report.
They show **where database time was actually spent** during the snapshot period.

These sections help answer a critical question:
What were sessions waiting for most of the time?

Correct interpretation allows fast identification of the dominant performance bottleneck.

---

## Top Timed Events

The Top Timed Events section lists wait events ordered by total time spent.

For each event, it typically shows:
- total wait time
- average wait time
- percentage of DB time
- associated wait class

This section highlights **symptoms**, not root causes.

---

## How to Read Top Timed Events

When analyzing Top Timed Events:
- focus on events with the highest percentage of DB time
- ignore idle wait events
- note the wait class associated with each event
- look for patterns rather than a single event

One or two events usually dominate the list.

---

## Interpreting Common Top Timed Events

### CPU Time

If CPU time dominates:
- sessions are actively running
- the system may be CPU-bound
- inefficient SQL is often involved

Next steps usually focus on SQL analysis and execution plans.

---

### User I/O Waits

Common User I/O events include:
- db file sequential read
- db file scattered read
- direct path read

Dominant User I/O waits usually indicate:
- inefficient access paths
- missing indexes
- large scans
- slow storage

Always correlate with SQL before blaming storage.

---

### Concurrency Waits

Concurrency-related events include:
- enq: TX - row lock contention
- buffer busy waits
- latch-related waits

High concurrency waits often point to:
- blocking sessions
- hot rows
- contention on shared structures

These waits require session-level investigation.

---

### Commit Waits

Commit-related waits appear when sessions wait for redo writes.

Common events include:
- log file sync

High commit waits usually indicate:
- slow redo log storage
- excessive commit frequency
- small redo logs

---

## Wait Classes in the AWR Report

Wait classes group related wait events and provide a higher-level view of bottlenecks.

Instead of analyzing individual waits, wait classes answer:
Which type of resource caused the most waiting?

---

## Key Wait Classes to Focus On

### CPU

- represents active execution time
- not shown as a wait event
- dominates when SQL is CPU-intensive or CPU is insufficient

High CPU usage often points to inefficient SQL or excessive concurrency.

---

### User I/O

- time spent waiting for data reads and writes
- strongly influenced by SQL access paths
- often dominates reporting workloads

User I/O dominance usually requires SQL and indexing review.

---

### Concurrency

- indicates contention between sessions
- includes locking and internal resource waits
- often sudden and workload-related

Concurrency dominance requires investigation of blocking and transaction behavior.

---

### Commit

- reflects commit-time redo waits
- common in OLTP systems
- sensitive to storage latency

Commit waits should be correlated with redo activity and application behavior.

---

### Configuration

- indicates configuration or sizing issues
- often related to space management or log switches

These waits usually require parameter or storage layout review.

---

## How Top Timed Events and Wait Classes Work Together

Top Timed Events show **specific symptoms**.
Wait Classes provide **context and direction**.

Together, they help:
- identify the dominant bottleneck
- avoid tuning the wrong area
- prioritize investigation steps

Always use both sections together.

---

## Common Analysis Mistakes

Avoid these mistakes:
- tuning based on a single wait event
- ignoring wait classes
- treating idle waits as problems
- assuming storage is slow without SQL evidence
- making parameter changes without validation

AWR analysis requires correlation, not assumptions.

---

## Practical Analysis Approach

A simple and effective method:
1. Review Top Timed Events
2. Identify dominant wait class
3. Determine bottleneck category (CPU, I/O, locks, commit)
4. Drill down into SQL and sessions
5. Validate findings with ASH or V$ views

This keeps analysis structured and efficient.

---

## Summary

- Top Timed Events show where DB time was spent
- wait classes group events into meaningful categories
- dominant wait classes identify the main bottleneck
- CPU, User I/O, Concurrency, and Commit are the most critical classes
- correct interpretation prevents incorrect tuning decisions

Top Timed Events and Wait Classes are the foundation of effective AWR analysis.
