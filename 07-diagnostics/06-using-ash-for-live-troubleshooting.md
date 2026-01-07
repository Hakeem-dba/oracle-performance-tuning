# Section 10.2 – Using ASH for Live Troubleshooting

## Overview

Active Session History (ASH) is one of the most effective tools for diagnosing **live or recently occurred performance problems**.
It allows DBAs to see what sessions are doing at a fine-grained level and to identify bottlenecks while the issue is happening.

This section explains how to use ASH during live troubleshooting and how to interpret its data correctly.

---

## When ASH Is the Right Tool

ASH should be used when:
- performance issues are currently occurring
- problems appear intermittently
- sessions are blocked or waiting
- response time suddenly degrades
- real-time visibility is required

For long-term or historical analysis, AWR is more appropriate.

---

## Basic ASH Query

The following query shows recent ASH activity for active sessions:

```sql
SELECT sample_time,
       session_id,
       session_serial#,
       sql_id,
       event,
       wait_class,
       blocking_session
FROM v$active_session_history
ORDER BY sample_time DESC;
```
- This provides a raw view of:
- which sessions were active
- what they were waiting for
- whether they were blocked

---

## Identifying Current Bottlenecks
### Group ASH Samples by Wait Class

To quickly identify the dominant bottleneck:
```sql
SELECT wait_class,
       COUNT(*) AS samples
FROM v$active_session_history
GROUP BY wait_class
ORDER BY samples DESC;
```
The wait class with the highest number of samples usually represents the primary bottleneck.

---

## Identifying Blocking Sessions
ASH makes it easy to identify blockers during live issues.
```sql
SELECT session_id,
       blocking_session,
       COUNT(*) AS samples
FROM v$active_session_history
WHERE blocking_session IS NOT NULL
GROUP BY session_id, blocking_session
ORDER BY samples DESC;
```
This shows:
- which sessions are being blocked
- which sessions are doing the blocking
- how frequently blocking occurs

---

## Identifying Problematic SQL

To find SQL statements contributing most to waits:
```sql
SELECT sql_id,
       COUNT(*) AS samples
FROM v$active_session_history
GROUP BY sql_id
ORDER BY samples DESC;
```
High sample counts usually indicate SQL that:
- runs for a long time
- is frequently executed
- causes waits or contention

This helps prioritize SQL for deeper analysis.

---

## CPU vs Waiting Sessions

ASH records both CPU usage and wait events.
- sessions with wait_class = 'CPU' are actively using CPU
- sessions with other wait classes are waiting for resources

This distinction helps determine whether the system is:
- CPU-bound
- I/O-bound
- lock-bound

---

## Correlating ASH with Time Windows

For intermittent issues, limit analysis to the affected time window:
```sql
SELECT wait_class,
       COUNT(*) AS samples
FROM v$active_session_history
WHERE sample_time BETWEEN
      TO_TIMESTAMP('2026-01-08 10:00:00','YYYY-MM-DD HH24:MI:SS')
  AND TO_TIMESTAMP('2026-01-08 10:15:00','YYYY-MM-DD HH24:MI:SS')
GROUP BY wait_class
ORDER BY samples DESC;
```
This helps isolate short-lived performance spikes.

## Common Use Cases for ASH

ASH is most effective when troubleshooting problems that are active, intermittent, or difficult to reproduce.

Typical use cases include:
- identifying blocking and lock contention in real time
- diagnosing sudden performance slowdowns
- analyzing concurrency issues during peak load
- confirming whether the bottleneck is CPU, I/O, or locks
- validating conclusions drawn from AWR reports

ASH provides session-level evidence when traditional metrics are not enough.

---

## Common Mistakes When Using ASH

Avoid these common mistakes:
- analyzing a single ASH sample instead of trends
- ignoring the time window in which the issue occurred
- focusing on SQL IDs without considering wait classes
- assuming ASH captures every short-lived event
- drawing conclusions without correlating with AWR or V$ views

ASH data should be interpreted statistically, not literally.

---

## Recommended Troubleshooting Flow Using ASH

A structured approach to using ASH effectively:

1. Confirm the performance issue is occurring or recently occurred  
2. Query ASH for the relevant time window  
3. Identify dominant wait classes  
4. Locate blocking sessions or high-impact SQL  
5. Correlate findings with AWR and V$ views  
6. Validate root cause before taking action  

This approach reduces guesswork and prevents unnecessary changes.

---

## Summary

- ASH is ideal for diagnosing live or short-lived performance issues
- it provides session-level visibility into waits and CPU usage
- grouping ASH samples reveals dominant bottlenecks
- ASH complements AWR by exposing issues AWR may miss
- effective use requires trend-based analysis and context

