# Section 10 – ASH Overview

## Overview

Active Session History (ASH) provides **near real-time visibility** into what database sessions are doing.
Unlike AWR, which aggregates data over long intervals, ASH captures **session activity at a fine-grained level**, making it ideal for diagnosing active or short-lived performance problems.

ASH answers the question:
What are sessions waiting for *right now* or during a specific recent period?

---

## What Is ASH?

ASH samples active sessions at regular intervals and records:
- session state (CPU or waiting)
- wait events and wait classes
- SQL ID and execution context
- objects being accessed
- blocking session information

Only **active sessions** are sampled:
- sessions using CPU
- sessions waiting for non-idle events

Idle sessions are not recorded.

---

## ASH vs AWR

Although related, ASH and AWR serve different purposes.

AWR:
- provides historical, aggregated performance data
- best for analyzing past issues
- compares workload between snapshot intervals

ASH:
- provides session-level visibility
- best for real-time or recent issues
- captures short spikes that AWR may miss

In practice, AWR and ASH are used together.

---

## When to Use ASH

ASH is most effective when:
- the problem is currently happening
- the issue occurs intermittently
- sessions are blocked or waiting
- response time suddenly degrades
- real-time troubleshooting is required

ASH is especially useful for diagnosing concurrency and wait-related problems.

---

## What Data ASH Captures

Each ASH sample includes:
- session ID and user
- SQL ID and execution ID
- wait event and wait class
- object and file/block information
- blocking session details
- program and module information

This allows precise correlation between sessions, SQL, and waits.

---

## ASH Sampling Behavior

By default:
- ASH samples active sessions approximately once per second
- samples are stored in memory and periodically flushed to AWR

Because sampling is periodic:
- very short events may not be captured
- patterns across many samples are more meaningful than individual rows

ASH should be interpreted statistically, not absolutely.

---

## Common Uses of ASH

ASH is commonly used to:
- identify which sessions are blocking others
- determine what SQL is currently slow
- analyze wait events in real time
- correlate performance issues with specific users or programs
- understand concurrency and contention patterns

ASH is one of the most effective tools for live troubleshooting.

---

## Accessing ASH Data

ASH data is available through:
- `V$ACTIVE_SESSION_HISTORY` (memory)
- `DBA_HIST_ACTIVE_SESS_HISTORY` (historical, via AWR)

The choice depends on whether the issue is current or historical.

---

## Licensing Consideration

ASH is part of the Oracle Diagnostics Pack.
It requires:
- Oracle Enterprise Edition
- Diagnostics Pack license enabled

ASH should not be used in environments where this license is not available.

---

## Best Practices When Using ASH

- focus on trends across samples, not single rows
- correlate ASH data with AWR and V$ views
- identify dominant wait classes and SQL IDs
- use ASH to confirm hypotheses, not guess causes
- avoid over-analyzing very short time windows

ASH is most powerful when combined with structured analysis.

---

## Summary

- ASH provides near real-time session-level performance data
- it captures active sessions only
- it complements AWR by revealing short-lived issues
- ASH is ideal for diagnosing live performance problems
- correct interpretation requires pattern-based analysis

ASH is a critical tool for understanding what the database is doing *right now*.
