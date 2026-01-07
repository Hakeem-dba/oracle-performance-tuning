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

Session A:
```sql
