# Section 11.2 – Join Methods

## Overview

Join methods define **how Oracle combines rows from multiple tables** to produce a result set.
The join method chosen by the optimizer has a major impact on performance, especially for queries involving large data sets.

Understanding join methods helps explain why a query is fast in one scenario and slow in another.

---

## Common Join Methods in Oracle

Oracle primarily uses three join methods:
- Nested Loops
- Hash Join
- Merge Join

Each method is efficient in different situations.
No join method is always the best choice.

---

## Nested Loops Join

### How It Works

In a nested loops join:
- Oracle selects a row from the driving table
- for each row, it probes the joined table
- this process repeats for all rows in the driving table

---

### When Nested Loops Is Efficient

Nested loops performs well when:
- the driving table returns few rows
- an index exists on the join column of the inner table
- OLTP-style queries are used

This is the most common join method for OLTP workloads.

---

### When Nested Loops Becomes a Problem

Nested loops can be inefficient when:
- the driving table returns many rows
- indexes are missing or unusable
- each probe results in many I/O operations

In such cases, execution time grows rapidly.

---

## Hash Join

### How It Works

In a hash join:
- Oracle builds a hash table in memory from one input
- the second input is scanned and matched using the hash table

If memory is insufficient, Oracle spills to TEMP.

---

### When Hash Join Is Efficient

Hash joins perform well when:
- large data sets are involved
- no useful indexes exist
- full scans are acceptable
- sufficient memory is available

Hash joins are common in reporting and batch workloads.

---

### When Hash Join Becomes a Problem

Hash joins can be inefficient when:
- memory is insufficient and TEMP is heavily used
- the build table is too large
- filtering could have reduced data earlier

TEMP usage is a key indicator of hash join pressure.

---

## Merge Join

### How It Works

In a merge join:
- both inputs are sorted on the join key
- Oracle scans both sorted inputs and merges matching rows

Sorting may require memory or TEMP.

---

### When Merge Join Is Efficient

Merge joins perform well when:
- both inputs are already sorted
- join columns are indexed
- large, ordered data sets are involved

They are less common but useful in specific scenarios.

---

### When Merge Join Becomes a Problem

Merge joins can be inefficient when:
- large sort operations are required
- TEMP usage becomes excessive
- input data is poorly filtered

---

## Choosing the Driving Table

The driving table is processed first and determines:
- join order
- amount of work performed
- overall query cost

A poor driving table choice often leads to inefficient joins.

The optimizer usually chooses the driving table based on estimated cardinality.

---

## Join Methods and Execution Plans

Execution plans clearly show:
- the chosen join method
- join order
- estimated row counts

When analyzing joins:
- confirm the join method makes sense for data volume
- compare estimated rows to actual rows
- look for unnecessary scans or sorts

---

## Common Join-Related Performance Issues

Typical problems include:
- nested loops on large data sets
- hash joins spilling heavily to TEMP
- merge joins with large sort operations
- incorrect join order due to bad statistics

Most join issues can be traced back to data volume and statistics.

---

## Summary

- join methods define how tables are combined
- nested loops suit small, indexed lookups
- hash joins suit large, unsorted data sets
- merge joins suit sorted or indexed inputs
- choosing the right join method is critical for performance

Understanding join methods is essential for interpreting and tuning execution plans.
