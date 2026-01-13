# Section 11.4 – Execution Plan Stability and Plan Changes

## Overview

Execution plan stability refers to Oracle consistently using an efficient execution plan for a SQL statement.
Unexpected execution plan changes are a common cause of sudden performance degradation in production systems.

Understanding why plans change and how to control them is a critical skill for SQL performance tuning.

---

## Why Execution Plans Change

Oracle execution plans are not fixed.
The optimizer may choose a different plan when its assumptions change.

Common reasons include:
- statistics refresh
- data volume growth
- data distribution changes (skew)
- bind variable peeking
- environment or parameter changes
- adaptive optimizer behavior

Plan changes are normal, but not always desirable.

---

## Statistics and Plan Changes

### Role of Optimizer Statistics

The optimizer relies heavily on statistics such as:
- table and index cardinality
- column selectivity
- data distribution
- system workload statistics

If statistics are stale, missing, or inaccurate, the optimizer may choose a poor plan.

---

### Statistics Refresh Impact

Refreshing statistics can:
- improve performance by enabling better plans
- introduce regressions by changing plan selection

Plan regressions after statistics collection are a common production issue.

Statistics should be gathered:
- during low activity periods
- using consistent methods
- with awareness of data volatility

---

## Data Growth and Skew

As data grows:
- access paths that were efficient may become expensive
- join methods may change
- full table scans may replace index access

Data skew can cause:
- incorrect cardinality estimates
- inefficient join order
- suboptimal access paths

These issues often appear gradually and then suddenly become visible.

---

## Bind Variables and Bind Peeking

Bind variables improve scalability, but they can affect plan stability.

Bind peeking occurs when:
- the optimizer uses the first bind value to choose a plan
- that plan is reused for different bind values

This can lead to:
- a good plan for some values
- a very poor plan for others

Bind-related issues are common in OLTP systems.

---

## Adaptive Optimizer Behavior

Modern Oracle versions use adaptive features such as:
- adaptive plans
- dynamic statistics
- SQL plan directives

These features allow Oracle to adjust execution behavior at runtime, but they can also:
- introduce unpredictability
- complicate plan analysis

Understanding adaptive behavior is important when diagnosing plan changes.

---

## Identifying Plan Changes

Execution plan changes can be detected using:
- AWR reports
- SQL ID history
- plan hash values
- SQL monitoring reports

Comparing old and new plans helps identify:
- what changed
- why performance changed
- whether the new plan is better or worse

---

## Common Symptoms of Plan Regression

Typical indicators include:
- sudden increase in response time
- increased CPU or I/O usage
- higher TEMP usage
- change in join method or access path
- same SQL behaving differently without code changes

Plan regression should always be considered when performance degrades suddenly.

---

## Strategies for Plan Stability

### SQL Design Best Practices

Stable SQL is easier to optimize.
Good practices include:
- writing clear and predictable SQL
- avoiding unnecessary complexity
- using consistent predicates
- minimizing reliance on implicit conversions

Good SQL design reduces optimizer uncertainty.

---

### Statistics Management

To improve plan stability:
- gather statistics consistently
- avoid unnecessary frequent stats collection
- use histograms only when justified
- monitor statistics changes on critical objects

Statistics management should be deliberate, not automatic.

---

### SQL Plan Management (SPM)

SQL Plan Management allows control over execution plans by:
- capturing known good plans
- preventing unverified plans from being used
- evolving plans in a controlled way

SPM is a powerful tool for protecting critical SQL from regressions.

---

## When to Enforce Plan Stability

Plan stability mechanisms should be used when:
- SQL is business-critical
- performance regressions have high impact
- execution plans fluctuate frequently
- root cause cannot be eliminated quickly

Not all SQL requires strict plan control.

---

## Common Mistakes

Avoid these mistakes:
- forcing plans without understanding the problem
- disabling optimizer features globally
- overusing hints
- ignoring data growth and workload evolution

Plan stability should balance control and flexibility.

---

## Summary

- execution plans can change due to statistics, data growth, and optimizer behavior
- plan changes are a common cause of sudden performance issues
- bind variables and adaptive features influence plan selection
- identifying plan changes requires historical comparison
- plan stability can be improved through design, statistics management, and SPM

Managing execution plan stability is essential for predictable SQL performance.
