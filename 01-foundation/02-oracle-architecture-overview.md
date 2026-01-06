# Section 2 – Oracle Architecture Overview

## Overview

Oracle Database performance is directly tied to its internal architecture.  
Understanding how the instance, memory, processes, and storage work together is essential for diagnosing and fixing performance problems.

This section explains the main Oracle architecture components and how each one affects performance.

---

## Oracle Instance vs Oracle Database

An Oracle Database consists of **two main parts**:

### Oracle Database (Physical)
The physical database includes:
- Datafiles
- Redo log files
- Control files
- TEMP files

These files store data on disk and are affected by storage latency and throughput.

---

### Oracle Instance (Memory + Processes)
The Oracle Instance consists of:
- Memory structures (SGA and PGA)
- Background processes
- Server processes

The instance is responsible for:
- Executing SQL
- Managing memory
- Reading and writing data
- Coordinating concurrent sessions

Performance issues usually occur at the **instance level**, not the physical files themselves.

---

## Memory Architecture Overview

Oracle uses memory to avoid expensive disk access.  
Memory access is measured in microseconds, while disk access is measured in milliseconds.

Efficient memory usage is one of the most important performance factors.

---

## System Global Area (SGA)

The SGA is a shared memory area used by all sessions connected to the database.

### Main SGA Components

#### Buffer Cache
- Stores frequently accessed data blocks
- Reduces physical reads from disk
- Improves read performance

If required blocks are not found in the buffer cache, Oracle must read them from disk, increasing response time.

---

#### Shared Pool
- Stores SQL statements
- Stores execution plans
- Stores data dictionary information

A well-sized shared pool reduces parsing overhead and CPU usage.

Poor shared pool usage often results in:
- High CPU
- Excessive hard parsing
- Performance instability

---

#### Large Pool
- Used for RMAN backups
- Used for parallel execution
- Used for shared server connections

If the large pool is too small, memory pressure is pushed to the shared pool, affecting SQL performance.

---

## Program Global Area (PGA)

The PGA is private memory allocated per session.

It is used for:
- Sorting operations
- Hash joins
- Bitmap operations
- Window functions
- Session-specific work areas

When PGA memory is insufficient, Oracle spills operations to the TEMP tablespace, causing significant performance degradation.

---

## Background Processes

Background processes perform critical tasks required for database stability and performance.

### Key Background Processes

#### DBWR (Database Writer)
- Writes dirty blocks from buffer cache to datafiles
- Frees memory in the buffer cache

Slow DBWR performance can cause:
- Buffer contention
- Slow DML operations
- Increased wait events

---

#### LGWR (Log Writer)
- Writes redo entries to redo log files
- Executes at commit time

Commit performance depends directly on LGWR speed and redo log storage performance.

---

#### CKPT (Checkpoint Process)
- Coordinates checkpoints
- Updates file headers
- Signals DBWR

Frequent checkpoints can increase I/O load and reduce performance.

---

#### SMON (System Monitor)
- Performs crash recovery
- Cleans up temporary segments
- Coalesces free space

SMON typically has minimal performance impact unless recovering from failure or heavy TEMP usage.

---

#### PMON (Process Monitor)
- Cleans up failed user processes
- Releases locks
- Frees resources

PMON ensures stability in environments with frequent session disconnects.

---

## Server Processes

Server processes execute SQL on behalf of user sessions.

They perform:
- SQL parsing
- Data block access
- Join and sort operations
- Result return to clients

High numbers of inefficient SQL statements increase server process workload and CPU usage.

---

## Database Storage Components

### Datafiles
- Store table and index data
- Affected by storage latency and throughput

Slow storage directly increases query response time.

---

### Redo Log Files
- Record all database changes
- Required for commits and recovery

Redo logs must be placed on fast, low-latency storage.

---

### Control Files
- Store metadata about database structure
- Required for database startup and recovery

---

## TEMP Tablespace

The TEMP tablespace is used when operations cannot be completed in PGA memory.

Common causes of TEMP usage:
- Sorting large result sets
- Hash joins on large tables
- Aggregations
- Parallel operations

High TEMP usage is often a sign of insufficient PGA or inefficient SQL.

---

## Why Architecture Knowledge Matters

Performance problems are rarely isolated issues.

Most slowdowns are caused by:
- Inefficient SQL
- Insufficient memory
- Slow I/O
- Concurrency issues
- Poor object design

Understanding Oracle architecture allows problems to be diagnosed correctly instead of applying random fixes.

---

## Summary

Oracle performance depends on:
- Efficient memory usage (SGA and PGA)
- Proper background process behavior
- Fast and stable storage
- Well-designed SQL and objects

A solid understanding of Oracle architecture is the foundation of effective performance troubleshooting.

---

## Next Section

**Section 3 – SGA Deep Dive**

The next section focuses on SGA components in detail and explains how buffer cache, shared pool, and large pool behavior affect performance.
