# Section 9 – AWR Overview

## Overview

The Automatic Workload Repository (AWR) is Oracle’s built-in performance data repository.
It captures and stores historical performance statistics that allow DBAs to analyze database behavior over time.

AWR is one of the most important tools for diagnosing performance problems, especially issues that are:
- intermittent
- workload-related
- no longer occurring at the time of investigation

---

## What Is AWR?

AWR automatically collects performance statistics at regular intervals called snapshots.

These statistics include:
- system-level metrics (CPU, memory, I/O)
- wait events and wait classes
- SQL execution statistics
- object usage statistics
- background process activity

Snapshots are stored in the SYSAUX tablespace and retained for a configurable period.

---

## Why AWR Is Important

AWR allows you to answer key performance questions such as:
- When did the performance problem start?
- What changed between two time periods?
- Was the issue CPU, I/O, memory, or lock related?
- Which SQL statements consumed the most resources?

Without AWR, diagnosing historical performance issues is extremely difficult.

---

## AWR Snapshots

### What Is a Snapshot?

A snapshot is a point-in-time capture of performance statistics.

By default:
- snapshots are taken every 60 minutes
- data is retained for 8 days

These settings can be adjusted based on requirements and storage considerations.

---

### What Data Is Captured

Each snapshot includes:
- wait event statistics
- CPU usage
- I/O statistics
- SQL execution metrics
- memory usage statistics
- load profile information

AWR does not capture query results or user data.

---

## AWR Report

An AWR report compares performance between two snapshots.
It highlights:
- top wait events
- top SQL by resource usage
- load profile changes
- instance efficiency indicators

The report provides direction for investigation, not final answers.

---

## When to Use AWR

AWR is most useful when:
- performance problems already occurred
- issues happen intermittently
- workload varies by time of day
- multiple users are affected
- real-time monitoring was not available

For real-time issues, ASH and V$ views are usually more appropriate.

---

## Common Sections in an AWR Report

Important sections to focus on:
- Load Profile
- Top Timed Events
- Wait Classes
- SQL ordered by CPU, elapsed time, and I/O
- Instance Efficiency Indicators

Understanding these sections helps avoid misinterpretation.

---

## Limitations of AWR

AWR has some limitations:
- snapshots may miss very short spikes
- data is aggregated, not session-level
- interpretation requires context
- licensing is required (Enterprise Edition with Diagnostics Pack)

AWR should be used together with other tools, not in isolation.

---

## Best Practices When Using AWR

- always compare similar workload periods
- avoid comparing peak and idle times
- correlate findings with application activity
- confirm AWR observations using V$ views or ASH
- treat AWR as a guide, not absolute truth

---

## Summary

- AWR stores historical performance data
- snapshots capture system and SQL statistics
- AWR reports compare two snapshots
- it is ideal for analyzing past performance problems
- correct interpretation is more important than report generation

AWR is the foundation of historical performance analysis in Oracle.
