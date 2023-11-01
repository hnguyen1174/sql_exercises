## 1. Table and Schema

```sql
Create table If Not Exists Products (product_id int, price int);
Create table If Not Exists Purchases (invoice_id int, product_id int, quantity int);
Truncate table Products;
insert into Products (product_id, price) values ('1', '100');
insert into Products (product_id, price) values ('2', '200');
Truncate table Purchases;
insert into Purchases (invoice_id, product_id, quantity) values ('1', '1', '2');
insert into Purchases (invoice_id, product_id, quantity) values ('3', '2', '1');
insert into Purchases (invoice_id, product_id, quantity) values ('2', '2', '3');
insert into Purchases (invoice_id, product_id, quantity) values ('2', '1', '4');
insert into Purchases (invoice_id, product_id, quantity) values ('4', '1', '10');
```

```
Write a solution to show the details of the invoice with the highest price. If two or more invoices have the same price, return the details of the one with the smallest invoice_id.

Return the result table in any order.

The result format is shown in the following example.

 

Example 1:

Input: 
Products table:
+------------+-------+
| product_id | price |
+------------+-------+
| 1          | 100   |
| 2          | 200   |
+------------+-------+
Purchases table:
+------------+------------+----------+
| invoice_id | product_id | quantity |
+------------+------------+----------+
| 1          | 1          | 2        |
| 3          | 2          | 1        |
| 2          | 2          | 3        |
| 2          | 1          | 4        |
| 4          | 1          | 10       |
+------------+------------+----------+
Output: 
+------------+----------+-------+
| product_id | quantity | price |
+------------+----------+-------+
| 2          | 3        | 600   |
| 1          | 4        | 400   |
+------------+----------+-------+
Explanation: 
Invoice 1: price = (2 * 100) = $200
Invoice 2: price = (4 * 100) + (3 * 200) = $1000
Invoice 3: price = (1 * 200) = $200
Invoice 4: price = (10 * 100) = $1000

The highest price is $1000, and the invoices with the highest prices are 2 and 4. We return the details of the one with the smallest ID, which is invoice 2.
```

## 2. SQL Answer

```sql
WITH invoice_value AS (
  SELECT
    p.invoice_id,
    SUM(p.quantity * pr.price) AS total_price
  FROM Purchases p
  LEFT JOIN Products pr
    ON p.product_id = pr.product_id
  GROUP BY 1
),

invoice_highest_price AS (
  SELECT
    invoice_id,
    RANK() OVER (ORDER BY total_price DESC) AS rank_num
  FROM invoice_value
),

invoice_highest_price_smaller_id AS (
  SELECT
    invoice_id,
    RANK() OVER (ORDER BY invoice_id ASC) AS rank_id
  FROM invoice_highest_price
  WHERE rank_num = 1
)

SELECT
  p.product_id,
  p.quantity,
  pr.price * p.quantity AS price
FROM Purchases p
LEFT JOIN Products pr
  ON p.product_id = pr.product_id
WHERE p.invoice_id = (SELECT invoice_id FROM invoice_highest_price_smaller_id WHERE rank_id = 1)
;
```

```sql
with temp as (
    select invoice_id, SUM(quantity*price) as 'price2'
    FROM Purchases a left join Products ON a.product_id=Products.product_id
    GROUP BY 1 
    ORDER BY 2 DESC, 1 ASC
    limit 1
)

SELECT a.product_id, quantity, quantity*b.price as 'price'
FROM Purchases a LEFT JOIN Products b ON a.product_id=b.product_id
WHERE invoice_id = (select invoice_id from temp)
```