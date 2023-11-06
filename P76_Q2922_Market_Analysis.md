## 1. Table Schema

```sql
Create table If Not Exists Users (seller_id int, join_date date, favorite_brand varchar(10));
Create table If Not Exists Orders (order_id int, order_date date, item_id int, seller_id int);
Create table If Not Exists Items (item_id int, item_brand varchar(10));
Truncate table Users;
insert into Users (seller_id, join_date, favorite_brand) values ('1', '2019-01-01', 'Lenovo');
insert into Users (seller_id, join_date, favorite_brand) values ('2', '2019-02-09', 'Samsung');
insert into Users (seller_id, join_date, favorite_brand) values ('3', '2019-01-19', 'LG');
Truncate table Orders;
insert into Orders (order_id, order_date, item_id, seller_id) values ('1', '2019-08-01', '4', '2');
insert into Orders (order_id, order_date, item_id, seller_id) values ('2', '2019-08-02', '2', '3');
insert into Orders (order_id, order_date, item_id, seller_id) values ('3', '2019-08-03', '3', '3');
insert into Orders (order_id, order_date, item_id, seller_id) values ('4', '2019-08-04', '1', '2');
insert into Orders (order_id, order_date, item_id, seller_id) values ('5', '2019-08-04', '4', '2');
Truncate table Items;
insert into Items (item_id, item_brand) values ('1', 'Samsung');
insert into Items (item_id, item_brand) values ('2', 'Lenovo');
insert into Items (item_id, item_brand) values ('3', 'LG');
insert into Items (item_id, item_brand) values ('4', 'HP');
```

```
Write a solution to find the top seller who has sold the highest number of unique items with a different brand than their favorite brand. If there are multiple sellers with the same highest count, return all of them.

Return the result table ordered by seller_id in ascending order.

The result format is in the following example.

 

Example 1:

Input: 
Users table:
+-----------+------------+----------------+
| seller_id | join_date  | favorite_brand |
+-----------+------------+----------------+
| 1         | 2019-01-01 | Lenovo         |
| 2         | 2019-02-09 | Samsung        |
| 3         | 2019-01-19 | LG             |
+-----------+------------+----------------+
Orders table:
+----------+------------+---------+-----------+
| order_id | order_date | item_id | seller_id |
+----------+------------+---------+-----------+
| 1        | 2019-08-01 | 4       | 2         |
| 2        | 2019-08-02 | 2       | 3         |
| 3        | 2019-08-03 | 3       | 3         |
| 4        | 2019-08-04 | 1       | 2         |
| 5        | 2019-08-04 | 4       | 2         |
+----------+------------+---------+-----------+
Items table:
+---------+------------+
| item_id | item_brand |
+---------+------------+
| 1       | Samsung    |
| 2       | Lenovo     |
| 3       | LG         |
| 4       | HP         |
+---------+------------+
Output: 
+-----------+-----------+
| seller_id | num_items |
+-----------+-----------+
| 2         | 1         |
| 3         | 1         |
+-----------+-----------+
Explanation: 
- The user with seller_id 2 has sold three items, but only two of them are not marked as a favorite. We will include a unique count of 1 because both of these items are identical.
- The user with seller_id 3 has sold two items, but only one of them is not marked as a favorite. We will include just that non-favorite item in our count.
Since seller_ids 2 and 3 have the same count of one item each, they both will be displayed in the output.
```

## 2. SQL Results

```sql
WITH sales AS (
  SELECT 
    u.seller_id,
    o.item_id,
    i.item_brand,
    u.favorite_brand
  FROM Users u
  LEFT JOIN Orders o
    ON u.seller_id = o.seller_id
  LEFT JOIN Items i
    ON o.item_id = i.item_id
),

results AS (
  SELECT 
    seller_id,
    COUNT(DISTINCT item_id) AS num_items
  FROM sales
  WHERE item_brand <> favorite_brand
  GROUP BY 1
)

SELECT 
  *
FROM results
WHERE num_items = (SELECT MAX(num_items) FROM results)
ORDER BY seller_id ASC
;
```