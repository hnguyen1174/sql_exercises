## 1. Table Schema

```sql
Create table If Not Exists Customers (customer_id int, name varchar(10));
Create table If Not Exists Orders (order_id int, order_date date, customer_id int, product_id int);
Create table If Not Exists Products (product_id int, product_name varchar(20), price int);
Truncate table Customers;
insert into Customers (customer_id, name) values ('1', 'Alice');
insert into Customers (customer_id, name) values ('2', 'Bob');
insert into Customers (customer_id, name) values ('3', 'Tom');
insert into Customers (customer_id, name) values ('4', 'Jerry');
insert into Customers (customer_id, name) values ('5', 'John');
Truncate table Orders;
insert into Orders (order_id, order_date, customer_id, product_id) values ('1', '2020-07-31', '1', '1');
insert into Orders (order_id, order_date, customer_id, product_id) values ('2', '2020-7-30', '2', '2');
insert into Orders (order_id, order_date, customer_id, product_id) values ('3', '2020-08-29', '3', '3');
insert into Orders (order_id, order_date, customer_id, product_id) values ('4', '2020-07-29', '4', '1');
insert into Orders (order_id, order_date, customer_id, product_id) values ('5', '2020-06-10', '1', '2');
insert into Orders (order_id, order_date, customer_id, product_id) values ('6', '2020-08-01', '2', '1');
insert into Orders (order_id, order_date, customer_id, product_id) values ('7', '2020-08-01', '3', '3');
insert into Orders (order_id, order_date, customer_id, product_id) values ('8', '2020-08-03', '1', '2');
insert into Orders (order_id, order_date, customer_id, product_id) values ('9', '2020-08-07', '2', '3');
insert into Orders (order_id, order_date, customer_id, product_id) values ('10', '2020-07-15', '1', '2');
Truncate table Products;
insert into Products (product_id, product_name, price) values ('1', 'keyboard', '120');
insert into Products (product_id, product_name, price) values ('2', 'mouse', '80');
insert into Products (product_id, product_name, price) values ('3', 'screen', '600');
insert into Products (product_id, product_name, price) values ('4', 'hard disk', '450');
```

```
Write a solution to find the most frequently ordered product(s) for each customer.

The result table should have the product_id and product_name for each customer_id who ordered at least one order.

Return the result table in any order.

The result format is in the following example.

 

Example 1:

Input: 
Customers table:
+-------------+-------+
| customer_id | name  |
+-------------+-------+
| 1           | Alice |
| 2           | Bob   |
| 3           | Tom   |
| 4           | Jerry |
| 5           | John  |
+-------------+-------+
Orders table:
+----------+------------+-------------+------------+
| order_id | order_date | customer_id | product_id |
+----------+------------+-------------+------------+
| 1        | 2020-07-31 | 1           | 1          |
| 2        | 2020-07-30 | 2           | 2          |
| 3        | 2020-08-29 | 3           | 3          |
| 4        | 2020-07-29 | 4           | 1          |
| 5        | 2020-06-10 | 1           | 2          |
| 6        | 2020-08-01 | 2           | 1          |
| 7        | 2020-08-01 | 3           | 3          |
| 8        | 2020-08-03 | 1           | 2          |
| 9        | 2020-08-07 | 2           | 3          |
| 10       | 2020-07-15 | 1           | 2          |
+----------+------------+-------------+------------+
Products table:
+------------+--------------+-------+
| product_id | product_name | price |
+------------+--------------+-------+
| 1          | keyboard     | 120   |
| 2          | mouse        | 80    |
| 3          | screen       | 600   |
| 4          | hard disk    | 450   |
+------------+--------------+-------+
Output: 
+-------------+------------+--------------+
| customer_id | product_id | product_name |
+-------------+------------+--------------+
| 1           | 2          | mouse        |
| 2           | 1          | keyboard     |
| 2           | 2          | mouse        |
| 2           | 3          | screen       |
| 3           | 3          | screen       |
| 4           | 1          | keyboard     |
+-------------+------------+--------------+
Explanation: 
Alice (customer 1) ordered the mouse three times and the keyboard one time, so the mouse is the most frequently ordered product for them.
Bob (customer 2) ordered the keyboard, the mouse, and the screen one time, so those are the most frequently ordered products for them.
Tom (customer 3) only ordered the screen (two times), so that is the most frequently ordered product for them.
Jerry (customer 4) only ordered the keyboard (one time), so that is the most frequently ordered product for them.
John (customer 5) did not order anything, so we do not include them in the result table.
```

```sql
WITH product_count AS (
  SELECT
    customer_id, 
    product_id,
    COUNT(*) AS product_pur
  FROM Orders
  GROUP BY 1,2
),

product_count_rank AS (
  SELECT
    *,
    RANK() OVER (PARTITION BY customer_id ORDER BY product_pur DESC) AS rn
  FROM product_count
)

SELECT 
  p.customer_id,
  p.product_id,
  pr.product_name
FROM product_count_rank p
LEFT JOIN Products pr
  ON p.product_id = pr.product_id
WHERE p.rn = 1
ORDER BY customer_id ASC, product_id ASC
;
```

```sql
-- Best
SELECT customer_id, product_id, product_name
FROM (
    SELECT O.customer_id, O.product_id, P.product_name, 
    RANK() OVER (PARTITION BY customer_id ORDER BY COUNT(O.product_id) DESC) AS rnk
    FROM Orders O
    JOIN Products P
    ON O.product_id = P.product_id  
    GROUP BY customer_id, product_id
) temp
WHERE rnk = 1 
ORDER BY customer_id, product_id
```

## 3. Pandas Schema

```python
import pandas as pd
import numpy as np

data = [[1, 'Alice'], [2, 'Bob'], [3, 'Tom'], [4, 'Jerry'], [5, 'John']]
customers = pd.DataFrame(data, columns=['customer_id', 'name']).astype({'customer_id':'Int64', 'name':'object'})
data = [[1, '2020-07-31', 1, 1], [2, '2020-7-30', 2, 2], [3, '2020-08-29', 3, 3], [4, '2020-07-29', 4, 1], [5, '2020-06-10', 1, 2], [6, '2020-08-01', 2, 1], [7, '2020-08-01', 3, 3], [8, '2020-08-03', 1, 2], [9, '2020-08-07', 2, 3], [10, '2020-07-15', 1, 2]]
orders = pd.DataFrame(data, columns=['order_id', 'order_date', 'customer_id', 'product_id']).astype({'order_id':'Int64', 'order_date':'datetime64[ns]', 'customer_id':'Int64', 'product_id':'Int64'})
data = [[1, 'keyboard', 120], [2, 'mouse', 80], [3, 'screen', 600], [4, 'hard disk', 450]]
products = pd.DataFrame(data, columns=['product_id', 'product_name', 'price']).astype({'product_id':'Int64', 'product_name':'object', 'price':'Int64'})
```

```python
most_frequently_ordered_prod = orders[['customer_id', 'product_id']].value_counts().reset_index()
maxes = most_frequently_ordered_prod.groupby(['customer_id'])['count'].max().reset_index()
tmp = pd.merge(
    most_frequently_ordered_prod,
    maxes,
    how='inner',
    on=['customer_id', 'count']
)
results = pd.merge(
    tmp, 
    products,
    on='product_id',
    how='left'
)[['customer_id', 'product_id', 'product_name']].sort_values(['customer_id', 'product_id'])
print(results)
```