## 1. SQL Schema

```sql
DROP TABLE IF EXISTS Orders;


CREATE TABLE Orders (
order_id INT,
product_id INT,
quantity INT,
purchase_date DATE
);


INSERT INTO Orders
VALUES
(1,1,7,'2020-03-16'),
(2,1,4,'2020-12-02'),
(3,1,7,'2020-05-10'),
(4,1,6,'2021-12-23'),
(5,1,5,'2021-05-21'),
(6,1,6,'2021-10-11'),
(7,2,6,'2022-10-11')
;
```

```
Write a solution to report the IDs of all the products that were ordered three or more times in two consecutive years.


Return the result table in any order.


The result format is shown in the following example.
+------------+
| product_id |
+------------+
| 1 |
+------------+
```

## 2. SQL Answer


```sql
SELECT DISTINCT d.product_id FROM
(SELECT product_id,
YEAR(purchase_date) AS curr_year,
LEAD(YEAR(purchase_date)) OVER(PARTITION BY product_id ORDER BY YEAR(purchase_date)) AS next_year
FROM orders
GROUP BY curr_year, product_id
HAVING COUNT(order_id) >= 3) d
WHERE d.next_year=d.curr_year+1
```

```sql
WITH next_years AS (
    SELECT
        product_id,
        YEAR(purchase_date) AS purchase_year,
        COUNT(DISTINCT order_id) AS order_count
    FROM Orders
    GROUP BY 1,2
    HAVING order_count >=3
)


SELECT
    DISTINCT c1.product_id
FROM next_years c1
JOIN next_years c2
    ON c1.product_id = c2.product_id
AND c1.purchase_year + 1 = c2.purchase_year
;
```
