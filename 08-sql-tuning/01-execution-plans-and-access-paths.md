# Section 11 – Execution Plans and Access Paths

## Overview

An execution plan describes **how Oracle executes a SQL statement**.
It shows the steps Oracle chooses to access data, join tables, and return results.

Understanding execution plans is essential for SQL tuning.
Most performance problems ultimately trace back to inefficient execution plans rather than hardware limitations.

This section explains how execution plans work, what access paths are, and how to interpret plans effectively.

---

## What Is an Execution Plan?

An execution plan is a tree of operations that Oracle follows to execute a SQL statement.

It describes:
- the order of operations
- how tables are accessed
- how rows are joined
- where filtering and sorting occur
- estimated cost and row counts

Execution plans are produced by the **Cost-Based Optimizer (CBO)**.

---

## What Is an Access Path?

An access path defines **how Oracle retrieves rows from a table**.

Common access paths include:
- full table scan
- index range scan
- index unique scan
- index full scan
- index fast full scan

The chosen access path has a major impact on performance.

---

## Full Table Scan

### Description

A full table scan reads all blocks of a table.

Oracle may choose a full table scan when:
- a large percentage of rows is required
- no usable index exists
- filtering is not selective
- parallel execution is used

---

### Performance Considerations

Full table scans are not always bad.
They can be efficient for:
- reporting queries
- batch processing
- small tables
- highly parallel workloads

Problems occur when full table scans are used unintentionally in OLTP systems.

---

## Index Access Paths

### Index Range Scan

Used when:
- the query returns a range of values
- the index predicate is selective

Common in OLTP workloads.

---

### Index Unique Scan

Used when:
- the query returns a single row
- a unique or primary key index is used

This is the most efficient index access path.

---

### Index Full Scan

Oracle scans the entire index in order.
Used when:
- the index contains all required columns
- sorting can be avoided

---

### Index Fast Full Scan

Reads the entire index without order.
Often used as an alternative to a full table scan when:
- the index is smaller than the table
- ordering is not required

---

## How Oracle Chooses an Execution Plan

The optimizer evaluates multiple possible plans and chooses the one with the lowest estimated cost.

Key factors include:
- object statistics
- data distribution
- index selectivity
- available memory
- join methods
- system workload

The chosen plan is based on estimates, not actual execution.

---

## Reading an Execution Plan

When reading a plan:
- read from bottom to top
- follow the indentation to understand join order
- identify access paths first
- then examine join methods
- finally review filtering and sorting steps

Understanding the plan structure is more important than memorizing plan operations.

---

## Estimated Rows vs Actual Rows

Execution plans include estimated row counts.
Large differences between estimated and actual rows often indicate:
- stale or missing statistics
- data skew
- incorrect assumptions by the optimizer

These differences are a common cause of poor performance.

---

## Common Execution Plan Problems

Typical issues include:
- full table scans in OLTP queries
- index usage on non-selective columns
- inefficient join order
- excessive sorting or hashing
- plan changes after statistics refresh

Most SQL tuning focuses on correcting these problems.

---

## Execution Plans and Performance Tuning

Execution plans help answer key questions:
- why is the SQL slow?
- where is most of the work happening?
- is Oracle accessing too much data?
- is the join strategy efficient?

Execution plans explain *why* SQL behaves the way it does.

---

## Summary

- execution plans describe how SQL is executed
- access paths define how data is retrieved
- full table scans and index scans have different use cases
- the optimizer chooses plans based on estimated cost
- understanding execution plans is essential for SQL tuning

Execution plans are the foundation of effective SQL performance analysis.
