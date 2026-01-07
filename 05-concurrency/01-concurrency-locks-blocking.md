# Section 7 – Concurrency, Locks, and Blocking

## Overview

Concurrency means many sessions are using the database at the same time.  
In OLTP systems, concurrency is normal and expected. Performance problems occur when sessions start waiting on each other because of locking, contention, or long-running transactions.

This section explains:
- what locks are and why Oracle uses them
- what blocking looks like in practice
- what deadlocks are and why they happen
- common causes of locking problems
- basic SQL queries used to identify blockers

---

## What Is Concurrency?

Concurrency is the ability of the database to handle multiple sessions at the same time, such as:
- many users inserting orders
- multiple services updating the same tables
- batch jobs running while online users are active

High concurrency is not a problem by itself.  
The problem starts when concurrency causes sessions to queue behind one another.

---

## Why Oracle Uses Locks

Locks are required to maintain data consistency.

Oracle uses locks to ensure:
- changes are isolated until commit or rollback
- two sessions do not modify the same data in conflicting ways
- reads and writes remain consistent

In most cases, Oracle locking is efficient and row-level.

---

## Basic Locking Behavior

### Reads (SELECT)
Normal SELECT statements do not block writers and are not blocked by writers in typical Oracle isolation behavior.

### Writes (INSERT / UPDATE / DELETE)
DML operations acquire locks and hold them until:
- COMMIT
- ROLLBACK
- session termination (cleanup occurs)

Locks are normally held only for the duration of a transaction.

---

## Blocking

Blocking occurs when one session holds a lock on a resource and another session needs a conflicting lock.

### Simple Example

Session A:
```sql
UPDATE accounts
SET balance = balance - 500
WHERE id = 10;
-- no commit yet
```
Session B:
```sql
UPDATE accounts
SET balance = balance + 500
WHERE id = 10;
```

Session B must wait until Session A commits or rolls back.

If Session A stays open for a long time, many sessions may queue behind it and the application can appear frozen.

# Common Symptoms and Diagnosis of Blocking

## Common Symptoms of Blocking

Blocking issues usually appear suddenly and are often reported as application slowness or system hangs.

Typical symptoms include:
- application screens stuck during updates or save operations
- sessions remaining in waiting state for long periods
- sudden increase in response time
- CPU usage remaining low while users complain of slowness
- transactions timing out at the application level

Blocking problems are often misdiagnosed as CPU or I/O issues, even though the root cause is lock contention.

---

## How Blocking Appears in the Database

When blocking occurs:
- one session holds a lock
- other sessions queue behind it
- blocked sessions do not consume CPU
- wait time increases while no work is performed

This makes the database appear idle from a CPU perspective while users experience delays.

---

## Common Wait Event for Blocking

The most common wait event associated with row-level blocking is:

- enq: TX - row lock contention

This wait event indicates that a session is waiting for another session to release a row-level lock.

---

## Identifying Blocked Sessions

### Find sessions that are blocked

```sql
SELECT sid,
       serial#,
       username,
       status,
       blocking_session,
       event
FROM v$session
WHERE blocking_session IS NOT NULL;
```

This query shows:
- sessions that are currently blocked
- the SID of the blocking session
- the wait event being experienced

## Identifying the Blocking Session

Once a blocked session is identified, the next step is to locate the blocking session.
Replace <SID> with the blocking session ID returned from the previous query.

```sql
SELECT sid,
       serial#,
       username,
       status,
       sql_id,
       event
FROM v$session
WHERE sid = <SID>;
```
This allows you to:
- confirm which session is causing the block
- see whether it is active or idle
- identify the SQL currently or previously executed

## Identifying the SQL Causing the Block

After identifying the SQL ID of the blocking session, retrieve the SQL text.
Replace <SQL_ID> with the actual SQL ID.

```sql
SELECT sql_id,
       sql_text
FROM v$sql
WHERE sql_id = '<SQL_ID>';
```
This step is critical for understanding why the lock is being held.

# Typical Causes and Response to Blocking

## Typical Causes Found During Diagnosis

Blocking issues are usually not random.  
They are almost always caused by application or workload behavior that holds locks longer than expected.

The most common causes include:

### Long-Running Transactions

- sessions performing updates and remaining open
- users leaving sessions idle without committing
- transactions that include slow external calls
- application logic that delays commits unnecessarily

Long-running transactions are the most frequent cause of blocking in OLTP systems.

---

### Missing Indexes on Foreign Keys

When a parent row is updated or deleted, Oracle must check for related child rows.

If the foreign key column is not indexed:
- Oracle may lock child table rows or perform full scans
- blocking and contention increase under concurrency

This is a very common real-world cause of blocking problems.

---

### Large Unfiltered Updates

Statements that affect a large number of rows hold locks for extended periods.

Example:

```sql
UPDATE orders
SET status = 'CLOSED';
```

Large updates should be:
- filtered carefully
- executed in batches where possible
- scheduled during low-usage periods

### Batch Jobs Executed During Peak Hours

Batch jobs are a common source of blocking in production systems.

They often:
- update or delete large numbers of rows
- run with parallel execution
- hold locks for extended periods
- compete with OLTP workloads for the same tables

When batch jobs run during business hours, online users may experience delays, timeouts, or application hangs due to lock contention.

---

#### Why Batch Jobs Cause Blocking

Batch jobs typically:
- process data in large transactions
- touch many rows in a single statement
- run longer than normal OLTP transactions

As a result, locks are held longer, increasing the likelihood that other sessions will be blocked.

---

#### Recommended Practices for Batch Jobs

To reduce blocking caused by batch workloads:

- schedule batch jobs during off-peak hours
- break large operations into smaller batches
- commit after each batch
- limit or disable parallel execution if locking becomes severe
- avoid running batch jobs against tables heavily used by OLTP applications

Batch workloads should be designed with concurrency in mind, not treated as isolated processes.

---

### Hot Rows

Hot rows are rows that are frequently updated by many sessions.

Examples include:
- counters
- status or flag rows
- configuration records
- summary rows updated on every transaction

Because many sessions target the same rows, contention and blocking occur even if each transaction is small.

---

### Impact of Hot Rows on Performance

Hot rows can cause:
- frequent row-level lock contention
- increased wait times
- reduced scalability
- unpredictable response times under load

This problem often appears suddenly when concurrency increases.

---

### Strategies to Reduce Hot Row Contention

Common approaches include:
- redesigning data models to distribute updates across multiple rows
- avoiding single-row counters when possible
- using sequences instead of manually updated counters
- aggregating data asynchronously instead of in real time

Reducing contention usually requires application-level design changes.

---

## Safe Response to Blocking Situations

When blocking is detected in production:

- identify the blocking session
- assess how long it has been running
- determine whether it is part of a batch or OLTP workload
- evaluate business impact before taking action

Immediate session termination should be avoided unless the impact is critical.

---

## Why Killing Sessions Is Risky

Killing a blocking session can:
- cause large rollbacks
- increase I/O and undo usage
- trigger application errors
- lead to repeated retries that worsen contention

Session termination treats the symptom, not the root cause.

---

## Long-Term Prevention

To prevent recurring blocking problems:

- keep batch and OLTP workloads separate
- design transactions to be short and efficient
- avoid hot-row designs
- index foreign key columns properly
- review batch job schedules regularly

Blocking prevention is primarily a design and workload management responsibility.

---

## Key Takeaways

- batch jobs running during peak hours are a common cause of blocking
- hot rows limit scalability and increase contention
- killing sessions should be a last resort
- long-term solutions focus on design, scheduling, and workload separation

## Reducing Blocking Safely

The correct solution to blocking problems depends on identifying the root cause rather than reacting to symptoms.

Common safe approaches include:
- keeping transactions short
- committing at appropriate points
- adding indexes on foreign key columns
- breaking large updates into smaller batches
- avoiding frequent updates to hot rows
- separating batch workloads from peak OLTP hours

Killing sessions should be considered a last resort, as it can trigger large rollbacks and cause application errors.

---

## Summary

- concurrency becomes a performance issue when sessions wait on each other
- locks protect data consistency but can cause blocking if held too long
- blocking is commonly caused by long transactions, large updates, or missing indexes
- deadlocks occur when sessions wait on each other in a cycle
- identifying blockers starts with V$SESSION and SQL investigation
