## 1. Table Schema

```sql
Create table If Not Exists Customers (customer_id int, name varchar(10));
Create table If Not Exists Orders (order_id int, order_date date, customer_id int, product_id int);
Create table If Not Exists Products (product_id int, product_name varchar(20), price int);
Truncate table Customers;
insert into Customers (customer_id, name) values ('1', 'Winston');
insert into Customers (customer_id, name) values ('2', 'Jonathan');
insert into Customers (customer_id, name) values ('3', 'Annabelle');
insert into Customers (customer_id, name) values ('4', 'Marwan');
insert into Customers (customer_id, name) values ('5', 'Khaled');
Truncate table Orders;
insert into Orders (order_id, order_date, customer_id, product_id) values ('1', '2020-07-31', '1', '1');
insert into Orders (order_id, order_date, customer_id, product_id) values ('2', '2020-7-30', '2', '2');
insert into Orders (order_id, order_date, customer_id, product_id) values ('3', '2020-08-29', '3', '3');
insert into Orders (order_id, order_date, customer_id, product_id) values ('4', '2020-07-29', '4', '1');
insert into Orders (order_id, order_date, customer_id, product_id) values ('5', '2020-06-10', '1', '2');
insert into Orders (order_id, order_date, customer_id, product_id) values ('6', '2020-08-01', '2', '1');
insert into Orders (order_id, order_date, customer_id, product_id) values ('7', '2020-08-01', '3', '1');
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
Write a solution to find the most recent order(s) of each product.

Return the result table ordered by product_name in ascending order and in case of a tie by the product_id in ascending order. If there still a tie, order them by order_id in ascending order.

The result format is in the following example.

 

Example 1:

Input: 
Customers table:
+-------------+-----------+
| customer_id | name      |
+-------------+-----------+
| 1           | Winston   |
| 2           | Jonathan  |
| 3           | Annabelle |
| 4           | Marwan    |
| 5           | Khaled    |
+-------------+-----------+
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
| 7        | 2020-08-01 | 3           | 1          |
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
+--------------+------------+----------+------------+
| product_name | product_id | order_id | order_date |
+--------------+------------+----------+------------+
| keyboard     | 1          | 6        | 2020-08-01 |
| keyboard     | 1          | 7        | 2020-08-01 |
| mouse        | 2          | 8        | 2020-08-03 |
| screen       | 3          | 3        | 2020-08-29 |
+--------------+------------+----------+------------+
Explanation: 
keyboard's most recent order is in 2020-08-01, it was ordered two times this day.
mouse's most recent order is in 2020-08-03, it was ordered only once this day.
screen's most recent order is in 2020-08-29, it was ordered only once this day.
The hard disk was never ordered and we do not include it in the result table.
```

## 2. SQL Answer

```sql
WITH order_product AS (
  SELECT
    *,
    RANK() OVER (PARTITION BY product_id ORDER BY order_date DESC) AS rn
  FROM Orders
)

SELECT 
  p.product_name,
  o.product_id,
  o.order_id,
  o.order_date
FROM order_product o
LEFT JOIN Products p
  ON o.product_id = p.product_id
WHERE o.rn = 1
ORDER BY p.product_name ASC, o.product_id ASC, o.order_id ASC
;
```

## 3. Pandas Schema

```python
import pandas as pd
import numpy as np

data = [[1, 'Winston'], [2, 'Jonathan'], [3, 'Annabelle'], [4, 'Marwan'], [5, 'Khaled']]
customers = pd.DataFrame(data, columns=['customer_id', 'name']).astype({'customer_id':'Int64', 'name':'object'})
data = [[1, '2020-07-31', 1, 1], [2, '2020-7-30', 2, 2], [3, '2020-08-29', 3, 3], [4, '2020-07-29', 4, 1], [5, '2020-06-10', 1, 2], [6, '2020-08-01', 2, 1], [7, '2020-08-01', 3, 1], [8, '2020-08-03', 1, 2], [9, '2020-08-07', 2, 3], [10, '2020-07-15', 1, 2]]
orders = pd.DataFrame(data, columns=['order_id', 'order_date', 'customer_id', 'product_id']).astype({'order_id':'Int64', 'order_date':'datetime64[ns]', 'customer_id':'Int64', 'product_id':'Int64'})
data = [[1, 'keyboard', 120], [2, 'mouse', 80], [3, 'screen', 600], [4, 'hard disk', 450]]
products = pd.DataFrame(data, columns=['product_id', 'product_name', 'price']).astype({'product_id':'Int64', 'product_name':'object', 'price':'Int64'})
```

## 4. Pandas Answer

```python
most_recent_orders_dates = orders.groupby('product_id')['order_date'].max().reset_index()
most_recent_orders = pd.merge(
    most_recent_orders_dates,
    orders,
    on=['product_id', 'order_date'],
    how='left'
)[['product_id', 'order_id', 'order_date']]

results = pd.merge(
    most_recent_orders,
    products,
    on='product_id',
    how='left'
)[['product_name', 'product_id', 'order_id', 'order_date']].sort_values(
    ['product_name', 'product_id', 'order_id']
)
print(results)
```