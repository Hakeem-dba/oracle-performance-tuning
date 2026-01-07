# Section 8 – Wait Classes Overview

## Overview

When Oracle sessions are not using CPU, they are waiting for something.
Wait events explain **what sessions are waiting for**, and wait classes group those waits into meaningful categories.

Understanding wait classes is a core skill for performance troubleshooting.
They help identify the **type of bottleneck** before diving into SQL, memory, or storage analysis.

---

## What Is a Wait Event?

A wait event represents time spent by a session waiting for a resource, such as:
- data from disk
- CPU availability
- a lock held by another session
- memory or internal structures

If a session is not actively running on CPU, it is waiting on a wait event.

---

## What Is a Wait Class?

A wait class is a logical grouping of related wait events.

Instead of analyzing hundreds of individual wait events, wait classes allow you to:
- quickly categorize performance problems
- focus on the correct area of investigation
- avoid guessing or random tuning

Wait classes answer the question:
**“What type of resource is the database waiting for?”**

---

## Why Wait Classes Matter

Wait classes provide immediate direction during performance issues.

They help you:
- distinguish between CPU, I/O, and locking problems
- avoid tuning the wrong component
- identify whether the issue is systemic or SQL-specific
- prioritize troubleshooting steps

Without wait classes, analysis often becomes trial-and-error.

---

## Major Wait Classes in Oracle

### CPU

The CPU class represents time spent actively executing on the CPU.
High CPU usage usually indicates:
- inefficient SQL
- excessive parsing
- high concurrency
- insufficient CPU resources

CPU problems are not visible as waits; they appear as active sessions consuming CPU.

---

### User I/O

User I/O waits occur when sessions wait for data blocks to be read from or written to disk.

Common examples:
- single-block reads (index access)
- multi-block reads (full table scans)
- direct path reads and writes

High User I/O often points to:
- inefficient SQL access paths
- missing indexes
- slow storage
- large data scans

---

### System I/O

System I/O waits are related to background process activity, such as:
- writing datafiles
- writing redo logs
- checkpoints

High System I/O may indicate:
- slow storage
- excessive redo generation
- frequent checkpoints

---

### Concurrency

Concurrency waits occur when sessions wait for internal database resources or locks.

Examples include:
- row-level locking
- latch contention
- enqueue waits

High concurrency waits often point to:
- blocking and locking problems
- hot rows
- high contention on shared structures

---

### Commit

Commit waits occur when sessions wait for redo information to be written to disk at commit time.

High commit waits often indicate:
- slow redo log storage
- excessive commit frequency
- small redo logs causing frequent switches

Commit-related issues are common in high-throughput OLTP systems.

---

### Configuration

Configuration waits indicate configuration-related bottlenecks, such as:
- space management issues
- log file switches
- undo or TEMP misconfiguration

These waits often point to setup or sizing problems rather than SQL issues.

---

### Administrative

Administrative waits are related to maintenance operations, such as:
- backups
- recovery
- management tasks

These waits are usually expected during administrative activities.

---

### Idle

Idle waits represent time when sessions are doing nothing useful.

Examples:
- waiting for client input
- idle background processes

Idle waits are **not performance problems** and should be ignored during analysis.

---

## How to Use Wait Classes in Troubleshooting

A simple and effective approach:

1. Identify the top wait classes
2. Determine which class dominates response time
3. Focus analysis on that area
4. Drill down into specific wait events and SQL

This approach prevents unnecessary tuning and speeds up root cause identification.

---

## Common Mistakes When Using Wait Classes

- ignoring CPU usage and focusing only on waits
- treating idle waits as performance issues
- jumping to storage tuning without SQL analysis
- analyzing individual waits without context

Wait classes should guide analysis, not replace deeper investigation.

---

## Summary

- wait events show what sessions are waiting for
- wait classes group related wait events
- wait classes help classify performance bottlenecks quickly
- CPU, I/O, concurrency, and commit are the most critical classes
- idle waits should be ignored
- effective troubleshooting starts with wait class analysis
