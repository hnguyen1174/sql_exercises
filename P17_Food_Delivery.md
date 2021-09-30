## Tables and Schema

```
SQL Schema
Table: Delivery

+-----------------------------+---------+
| Column Name                 | Type    |
+-----------------------------+---------+
| delivery_id                 | int     |
| customer_id                 | int     |
| order_date                  | date    |
| customer_pref_delivery_date | date    |
+-----------------------------+---------+
delivery_id is the primary key of this table.
The table holds information about food delivery to customers that make orders at some date and specify a preferred delivery date (on the same order date or after it).
```

## Question 1:

If the preferred delivery date of the customer is the same as the order date then the order is called immediate otherwise it's called scheduled.

Write an SQL query to find the percentage of immediate orders in the table, rounded to 2 decimal places.

The query result format is in the following example:

```sql
SELECT
    ROUND(SUM(CASE WHEN order_date = customer_pref_delivery_date THEN 1 ELSE 0 END)*100/COUNT(delivery_id), 2) AS immediate_percentage
FROM Delivery
```

## Question 2:

```sql
SELECT 
    ROUND(SUM(CASE WHEN order_date = customer_pref_delivery_date THEN 1 ELSE 0 END)*100/COUNT(delivery_id), 2) AS immediate_percentage
FROM (
SELECT 
    customer_id,
    RANK() OVER (PARTITION BY customer_id ORDER BY order_date) AS customer_order_rank,
    delivery_id,
    order_date,
    customer_pref_delivery_date
FROM Delivery
) TMP
WHERE customer_order_rank = 1
```
