# Outlier Treatment

## Month 5 | Day 11

Today I learned and practiced the fundamentals of **outlier detection and treatment** using Pandas, NumPy, and Seaborn. I worked with the `MonthlyIncome` column from the HR Analytics dataset and learned how to identify, visualize, trim, and cap extreme values.

### 1. What is an Outlier?

An outlier is a data point that is unusually far from the other observations in a dataset. Outliers can affect statistical analysis, mean, standard deviation, data visualization, and machine learning models.

### 2. IQR Method

The Interquartile Range (IQR) is a common method for detecting potential outliers.

**Formula:**

`IQR = Q3 - Q1`

Where:

- Q1 = 25th percentile
- Q3 = 75th percentile

**Lower and Upper Bounds:**

`Lower Bound = Q1 - 1.5 × IQR`

`Upper Bound = Q3 + 1.5 × IQR`

For `MonthlyIncome`:

```python
Q1 = df["MonthlyIncome"].quantile(0.25)
Q3 = df["MonthlyIncome"].quantile(0.75)

IQR = Q3 - Q1

lower_bound = Q1 - 1.5 * IQR
upper_bound = Q3 + 1.5 * IQR
