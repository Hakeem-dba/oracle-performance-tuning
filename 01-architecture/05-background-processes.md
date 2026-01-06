# Section 5 – Background Processes

## Overview

Oracle background processes are internal processes that run as part of the database instance.  
They are responsible for managing memory, writing data to disk, maintaining consistency, and ensuring database stability.

Although these processes operate in the background, their behavior has a direct and measurable impact on database performance.

---

## What Are Background Processes?

Background processes perform essential system tasks that allow user sessions to run efficiently.

They handle operations such as:
- Writing modified data to disk
- Managing redo generation
- Coordinating checkpoints
- Cleaning up failed sessions
- Performing recovery tasks

Without background processes, the Oracle database cannot function correctly.

---

## Key Background Processes

### DBWR – Database Writer

#### Purpose
DBWR writes modified (dirty) data blocks from the buffer cache to datafiles on disk.

It ensures:
- Data durability
- Availability of free buffers in memory
- Efficient use of buffer cache

---

#### Performance Impact

If DBWR cannot write dirty blocks fast enough:
- Buffer cache fills with dirty blocks
- New data cannot be read into memory
- Sessions experience delays

Common symptoms include:
- Slow INSERT and UPDATE operations
- Increased wait events related to buffer availability
- Storage I/O pressure

---

#### Common Causes of DBWR Bottlenecks

- Slow storage subsystem
- High volume of concurrent DML
- Small buffer cache
- Frequent checkpoints

---

## LGWR – Log Writer

### Purpose

LGWR writes redo entries from memory to redo log files.

Redo records all changes made to the database and is required for:
- Commit operations
- Crash recovery
- Data consistency

LGWR writes occur at commit time.

---

### Performance Impact

Commit performance depends directly on LGWR speed.

If LGWR is slow:
- Commits take longer
- Applications appear slow or unresponsive
- User sessions wait for redo writes to complete

Redo log storage latency is one of the most critical performance factors in OLTP systems.

---

### Common Causes of LGWR Bottlenecks

- Redo logs on slow storage
- Small redo log files causing frequent log switches
- Excessive commit frequency
- Storage contention

---

## CKPT – Checkpoint Process

### Purpose

CKPT coordinates checkpoints by:
- Signaling DBWR to write dirty buffers
- Updating datafile headers
- Updating control file information

CKPT does not write data blocks itself.

---

### Performance Impact

Frequent checkpoints increase I/O activity and can affect performance.

Common reasons for excessive checkpoints include:
- Small redo log file size
- High redo generation rate

Reducing checkpoint frequency helps stabilize write I/O.

---

## SMON – System Monitor

### Purpose

SMON performs system-level maintenance tasks such as:
- Instance recovery after crashes
- Cleaning up temporary segments
- Coalescing free space

---

### Performance Impact

SMON usually has minimal impact on performance.

However, increased SMON activity may occur:
- After database crashes
- During heavy TEMP usage
- When cleaning large temporary segments

Excessive TEMP usage often leads to increased SMON workload.

---

## PMON – Process Monitor

### Purpose

PMON manages failed user processes by:
- Releasing locks held by terminated sessions
- Cleaning up session resources
- Freeing memory
- Maintaining system stability

---

### Performance Impact

PMON helps prevent long-term blocking and resource leaks.

In environments with unstable connections or application crashes, PMON activity becomes more frequent.

---

## Server Processes vs Background Processes

Server processes:
- Execute SQL for user sessions
- Perform parsing, reading, joining, and sorting
- Return results to clients

Background processes:
- Support system operations
- Manage memory and disk I/O
- Maintain consistency and recovery

High server process load often reveals inefficient SQL, while background process stress often points to I/O or memory issues.

---

## Observing Background Process Behavior

Performance symptoms related to background processes often appear as:
- Slow commits
- Slow DML operations
- Increased wait events
- I/O saturation
- Checkpoint-related delays

Background processes should be analyzed together with wait events and I/O statistics.

---

## Why Background Processes Matter in Performance Tuning

Many performance problems are not caused by SQL directly, but by how background processes handle workload pressure.

Understanding background process behavior allows:
- Faster diagnosis of root causes
- Better memory and storage tuning decisions
- Improved system stability

---

## Summary

- Background processes handle essential database operations
- DBWR affects write performance and buffer availability
- LGWR affects commit performance
- CKPT influences checkpoint frequency and I/O load
- SMON and PMON ensure recovery and stability
- Performance issues often manifest through background process behavior

Understanding these processes is essential for effective Oracle performance troubleshooting.

---

## Next Section

**Section 6 – I/O and Wait Events**

The next section explains how Oracle waits occur, how I/O impacts performance, and how wait events help identify bottlenecks.
