## Table and Schema

```
-- Table: Customer
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| customer_id   | int     |
| name          | varchar |
| visited_on    | date    |
| amount        | int     |
+---------------+---------+
(customer_id, visited_on) is the primary key for this table.
This table contains data about customer transactions in a restaurant.
visited_on is the date on which the customer with ID (customer_id) have visited the restaurant.
amount is the total paid by a customer.
```

```
Customer table:
+-------------+--------------+--------------+-------------+
| customer_id | name         | visited_on   | amount      |
+-------------+--------------+--------------+-------------+
| 1           | Jhon         | 2019-01-01   | 100         |
| 2           | Daniel       | 2019-01-02   | 110         |
| 3           | Jade         | 2019-01-03   | 120         |
| 4           | Khaled       | 2019-01-04   | 130         |
| 5           | Winston      | 2019-01-05   | 110         | 
| 6           | Elvis        | 2019-01-06   | 140         | 
| 7           | Anna         | 2019-01-07   | 150         |
| 8           | Maria        | 2019-01-08   | 80          |
| 9           | Jaze         | 2019-01-09   | 110         | 
| 1           | Jhon         | 2019-01-10   | 130         | 
| 3           | Jade         | 2019-01-10   | 150         | 
+-------------+--------------+--------------+-------------+

Result table:
+--------------+--------------+----------------+
| visited_on   | amount       | average_amount |
+--------------+--------------+----------------+
| 2019-01-07   | 860          | 122.86         |
| 2019-01-08   | 840          | 120            |
| 2019-01-09   | 840          | 120            |
| 2019-01-10   | 1000         | 142.86         |
+--------------+--------------+----------------+
```

## Question 1: SQL

You are the restaurant owner and you want to analyze a possible expansion (there will be at least one customer every day).

Write an SQL query to compute moving average of how much customer paid in a 7 days window (current day + 6 days before) .

The query result format is in the following example:

Return result table ordered by visited_on.

average_amount should be rounded to 2 decimal places, all dates are in the format ('YYYY-MM-DD').

```sql
# Write your MySQL query statement below

-- Step 1: Get the sum total amount for each day
-- Step 2: Using windows function to get sum amount for the current day and 6 days before
-- Step 3: Average and round by 2, select only from row 7 forward]

WITH CTE AS (
    SELECT 
        visited_on,
        SUM(amount) as total_amount
    FROM Customer
    GROUP BY visited_on
    ORDER BY visited_on
)

SELECT 
    visited_on,
    amount,
    ROUND(amount/7, 2) AS average_amount
FROM (
SELECT 
    visited_on,
    SUM(total_amount) OVER (ORDER BY visited_on ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) AS amount,
    ROW_NUMBER() OVER (ORDER BY visited_on) AS row_no
FROM CTE
) TMP
WHERE row_no > 6
```

## Question 2: Python

```python 
import pandas as pd
import numpy as np

cutomer_id = [
    1, 2, 3, 4, 5, 6,
    7, 8, 9, 1, 3
]

name = [
    'Jhon', 'Daniel', 'Jade',
    'Khaled', 'Winston', 'Elvis',
    'Anna', 'Maria', 'Jaze', 'Jhon',
    'Jade'
]

visited_on = [
    '2019-01-01', '2019-01-02', '2019-01-03',
    '2019-01-04', '2019-01-05', '2019-01-06',
    '2019-01-07', '2019-01-08', '2019-01-09',
    '2019-01-10', '2019-01-10'
]

amount = [
    100, 110, 120, 130, 110,
    140, 150, 80, 110, 130, 150
]

df = pd.DataFrame({
    'customer_id': cutomer_id,
    'name': name,
    'visited_on': visited_on,
    'amount': amount
})

df['visited_on'] = pd.to_datetime(df['visited_on'])
df_by_date = df.groupby('visited_on')['amount'].sum().reset_index()
df_result = pd.DataFrame({
    'visited_on': df_by_date['visited_on'],
    'amount': df_by_date['amount'].rolling(7).sum(),
    'average_amount': df_by_date['amount'].rolling(7).mean().round(2)
})
df_result = df_result.dropna().reset_index().drop('index', axis=1)
print(df_result)
```
