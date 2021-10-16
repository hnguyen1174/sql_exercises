## Table and Schema

```
Table: Accounts

+-------------+------+
| Column Name | Type |
+-------------+------+
| account_id  | int  |
| income      | int  |
+-------------+------+
account_id is the primary key for this table.
Each row contains information about the monthly income for one bank account.
```

Write an SQL query to report the number of bank accounts of each salary category. The salary categories are:

"Low Salary": All the salaries strictly less than $20000.
"Average Salary": All the salaries in the inclusive range [$20000, $50000].
"High Salary": All the salaries strictly greater than $50000.
The result table must contain all three categories. If there are no accounts in a category, then report 0. Return the result table in any order.

The query result format is in the following example.

```
Accounts table:
+------------+--------+
| account_id | income |
+------------+--------+
| 3          | 108939 |
| 2          | 12747  |
| 8          | 87709  |
| 6          | 91796  |
+------------+--------+

Result table:
+----------------+----------------+
| category       | accounts_count |
+----------------+----------------+
| Low Salary     | 1              |
| Average Salary | 0              |
| High Salary    | 3              |
+----------------+----------------+

Low Salary: Account 2.
Average Salary: No accounts.
High Salary: Accounts 3, 6, and 8.
```

## SQL

```sql
WITH CTE AS (
    SELECT *, 
    CASE WHEN income < 20000 THEN 'Low Salary'
    WHEN income > 50000 THEN 'High Salary'
    ELSE 'Average Salary' END AS category
    FROM Accounts
)

(SELECT 
    'Low Salary' AS category,
    SUM(CASE WHEN category = 'Low Salary' THEN 1 ELSE 0 END) AS accounts_count
FROM CTE)
UNION
SELECT 
    'Average Salary' AS category,
    SUM(CASE WHEN category = 'Average Salary' THEN 1 ELSE 0 END) AS accounts_count
FROM CTE
UNION
SELECT 
    'High Salary' AS category,
    SUM(CASE WHEN category = 'High Salary' THEN 1 ELSE 0 END) AS accounts_count
FROM CTE
```

## Python 
