# GroupBy & Aggregation

## What is GroupBy?
`groupby()` divides a DataFrame into groups based on one or more columns and allows calculations to be performed on each group.

## Why Use GroupBy?
GroupBy helps answer business questions about groups or categories, such as:
- What is the average salary per department?
- How many employees are in each department?
- What is the total salary by department?
- Which department has the highest average salary?

## Split → Apply → Combine

```text
Split  →  Divide data into groups
Apply  →  Perform a calculation on each group
Combine → Return the summarized results