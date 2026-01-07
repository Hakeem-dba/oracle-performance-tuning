# Section 8.2 – CPU vs I/O vs Locks

## Overview

One of the first questions in performance troubleshooting is:
**Is the database slow because of CPU, I/O, or locking?**

Answering this early prevents wasted effort and incorrect tuning.
This section explains how to distinguish between CPU-bound, I/O-bound, and lock-bound systems using symptoms and wait analysis.

---

## CPU-Bound Systems

### What CPU-Bound Means

A system is CPU-bound when sessions are ready to run but cannot get CPU time.
Work exists, but CPU resources are saturated.

---

### Common Symptoms

- high CPU utilization at the OS level
- many active sessions running on CPU
- low I/O wait time
- response time degrades as concurrency increases

CPU-bound systems often appear “busy” rather than “stuck”.

---

### Common Causes

- inefficient SQL consuming excessive CPU
- high hard-parse rates
- excessive concurrency
- insufficient CPU cores
- application loops or repeated queries

---

### Typical Investigation Direction

- identify top CPU-consuming SQL
- review execution plans
- check parsing behavior
- reduce unnecessary workload
- evaluate CPU capacity

---

## I/O-Bound Systems

### What I/O-Bound Means

A system is I/O-bound when sessions spend most of their time waiting for data to be read from or written to disk.

The CPU may be mostly idle while sessions wait for storage.

---

### Common Symptoms

- low to moderate CPU usage
- high User I/O or System I/O wait classes
- queries slow only when accessing data
- TEMP usage may increase
- performance improves when workload is reduced

---

### Common Causes

- full table scans on large tables
- missing or inefficient indexes
- large data volumes processed
- slow storage or high latency
- excessive TEMP spills

---

### Typical Investigation Direction

- identify SQL with high I/O waits
- review execution plans
- reduce data access volume
- check storage latency
- optimize indexing and filtering

---

## Lock-Bound (Concurrency-Bound) Systems

### What Lock-Bound Means

A system is lock-bound when sessions are waiting on other sessions rather than on CPU or I/O.

Work cannot proceed because required locks are held.

---

### Common Symptoms

- low CPU usage
- many sessions in waiting state
- response time increases suddenly
- applications appear frozen
- blocking-related wait events dominate

---

### Common Causes

- long-running transactions
- missing indexes on foreign keys
- large update statements
- batch jobs running during peak hours
- hot rows updated frequently

---

### Typical Investigation Direction

- identify blocking sessions
- review transaction duration
- inspect locking SQL
- evaluate workload overlap
- correct application or scheduling behavior

---

## Comparing the Three Bottlenecks

| Bottleneck Type | CPU Usage | I/O Waits | Blocking |
|-----------------|----------|-----------|----------|
| CPU-bound       | High     | Low       | Low      |
| I/O-bound       | Low/Med  | High      | Low      |
| Lock-bound      | Low      | Low       | High     |

This comparison helps quickly narrow the investigation path.

---

## Why This Classification Matters

Misclassifying the bottleneck leads to incorrect tuning:
- adding indexes won’t fix locking
- increasing memory won’t fix CPU saturation
- upgrading storage won’t fix bad SQL

Correct classification saves time and avoids unnecessary changes.

---

## Simple Troubleshooting Flow

1. Check CPU usage
2. Review top wait classes
3. Identify dominant bottleneck
4. Focus analysis on the correct layer
5. Drill down into SQL or workload behavior

This approach keeps troubleshooting structured and efficient.

---

## Summary

- CPU-bound systems suffer from insufficient CPU or inefficient SQL
- I/O-bound systems wait on storage due to heavy or inefficient data access
- lock-bound systems wait on other sessions holding locks
- wait classes help identify the dominant bottleneck quickly
- correct classification is essential before tuning

Understanding whether the problem is CPU, I/O, or locks is the foundation of effective performance troubleshooting.
