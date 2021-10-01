## Tables and Schema

```
Table: Transactions

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| id            | int     |
| country       | varchar |
| state         | enum    |
| amount        | int     |
| trans_date    | date    |
+---------------+---------+
id is the primary key of this table.
The table has information about incoming transactions.
The state column is an enum of type ["approved", "declined"].
```

## Question 1: Write an SQL query to find for each month and country, the number of transactions and their total amount, the number of approved transactions and their total amount.

```sql
SELECT
    DATE_FORMAT(trans_date, '%Y-%m') AS month,
    country,
    COUNT(DISTINCT id) AS trans_count,
    SUM(CASE WHEN state = 'approved' THEN 1 ELSE 0 END) AS approved_count,
    SUM(amount) AS trans_total_amount,
    SUM(CASE WHEN state = 'approved' THEN amount ELSE 0 END) AS approved_total_amount
FROM Transactions
GROUP BY DATE_FORMAT(trans_date, '%Y-%m'), country
```

## Question 2: Write an SQL query to find for each month and country: the number of approved transactions and their total amount, the number of chargebacks, and their total amount.

Note: In your query, given the month and country, ignore rows with all zeros.

The query result format is in the following example:

```sql

SELECT 
    DATE_FORMAT(trans_date, '%Y-%m') AS month, 
    country,
    SUM(CASE WHEN state = 'approved' THEN 1 ELSE 0 END) AS approved_count,
    SUM(CASE WHEN state = 'approved' THEN amount ELSE 0 END) AS approved_amount,
    SUM(CASE WHEN state = 'chargeback' THEN 1 ELSE 0 END) AS chargeback_count,
    SUM(CASE WHEN state = 'chargeback' THEN amount ELSE 0 END) AS chargeback_amount
FROM (
SELECT * FROM Transactions
UNION
(
    SELECT 
        c.trans_id AS id,
        t.country AS country,
        'chargeback' AS state,
        t.amount AS amount,
        c.trans_date AS trans_date
    FROM Transactions t
    JOIN Chargebacks c
    ON t.id = c.trans_id
)
) TMP
GROUP BY DATE_FORMAT(trans_date, '%Y-%m'), country
HAVING approved_count > 0 
OR approved_amount > 0
OR chargeback_count > 0
OR chargeback_amount > 0
ORDER BY DATE_FORMAT(trans_date, '%Y-%m'), country
```
