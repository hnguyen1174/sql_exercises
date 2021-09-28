## Tables and Schema

```
Table: Users

+----------------+---------+
| Column Name    | Type    |
+----------------+---------+
| user_id        | int     |
| join_date      | date    |
| favorite_brand | varchar |
+----------------+---------+
user_id is the primary key of this table.
This table has the info of the users of an online shopping website where users can sell and buy items.
```

```
Table: Orders

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| order_id      | int     |
| order_date    | date    |
| item_id       | int     |
| buyer_id      | int     |
| seller_id     | int     |
+---------------+---------+
order_id is the primary key of this table.
item_id is a foreign key to the Items table.
buyer_id and seller_id are foreign keys to the Users table.
```

```
Table: Items

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| item_id       | int     |
| item_brand    | varchar |
+---------------+---------+
item_id is the primary key of this table.
```

## Question 1

Write an SQL query to find for each user, the join date and the number of orders they made as a buyer in 2019.

```sql
SELECT 
    u.user_id AS buyer_id,
    u.join_date AS join_date,
    COUNT(DISTINCT o.order_id) AS orders_in_2019
FROM Users u
LEFT JOIN (
    SELECT * FROM Orders
    WHERE order_date BETWEEN DATE('2019-01-01') AND DATE('2019-12-31')
)  o
ON u.user_id = o.buyer_id
GROUP BY u.user_id, u.join_date
```

## Question 2

Write an SQL query to find for each user, whether the brand of the second item (by date) they sold is their favorite brand. If a user sold less than two items, report the answer for that user as no.

It is guaranteed that no seller sold more than one item on a day.

```sql
SELECT 
    seller_id,
    CASE WHEN order_count < 2 THEN 'no'
    WHEN favorite_brand = item_brand THEN 'yes'
    ELSE 'no' END AS 2nd_item_fav_brand
FROM (
SELECT 
    u.user_id AS seller_id,
    u.favorite_brand AS favorite_brand,
    i.item_brand AS item_brand,
    RANK() OVER (PARTITION BY o.seller_id ORDER BY o.order_date) AS item_rank,
    COUNT(order_id) OVER (PARTITION BY o.seller_id) AS order_count
FROM Users u
LEFT JOIN Orders o
ON u.user_id = o.seller_id
LEFT JOIN Items i
ON o.item_id = i.item_id
) TMP
WHERE item_rank = 2 
OR order_count < 2
```


