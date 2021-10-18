## Table and Schema

```sql
CREATE TABLE Orders (
  order_id INT, 
  customer_id INT,
  order_date DATE,
  item_id INT,
  quantity INT
);

INSERT INTO Orders
VALUES 
(1, 1, '2020-06-01', 1, 10),
(2, 1, '2020-06-08', 2, 10),
(3, 2, '2020-06-02', 1, 5),
(4, 3, '2020-06-03', 3, 5),
(5, 4, '2020-06-04', 4, 1),
(6, 4, '2020-06-05', 5, 5),
(7, 5, '2020-06-05', 1, 10),
(8, 5, '2020-06-14', 4, 5),
(9, 5, '2020-06-21', 3, 5);

CREATE TABLE Items (
  item_id INT, 
  item_name VARCHAR(20),
  item_category VARCHAR(20)
);

INSERT INTO Items
VALUES 
(1, "LC Alg. Book", "Book"),
(2, "LC DB. Book", "Book"),
(3, "LC SmarthPhone", "Phone"),
(4, "LC Phone 2020", "Phone"),
(5, "LC SmartGlass", "Glasses"),
(6, "LC T-Shirt XL", "T-Shirt");
```

```
Orders table:
+------------+--------------+-------------+--------------+-------------+
| order_id   | customer_id  | order_date  | item_id      | quantity    |
+------------+--------------+-------------+--------------+-------------+
| 1          | 1            | 2020-06-01  | 1            | 10          |
| 2          | 1            | 2020-06-08  | 2            | 10          |
| 3          | 2            | 2020-06-02  | 1            | 5           |
| 4          | 3            | 2020-06-03  | 3            | 5           |
| 5          | 4            | 2020-06-04  | 4            | 1           |
| 6          | 4            | 2020-06-05  | 5            | 5           |
| 7          | 5            | 2020-06-05  | 1            | 10          |
| 8          | 5            | 2020-06-14  | 4            | 5           |
| 9          | 5            | 2020-06-21  | 3            | 5           |
+------------+--------------+-------------+--------------+-------------+

Items table:
+------------+----------------+---------------+
| item_id    | item_name      | item_category |
+------------+----------------+---------------+
| 1          | LC Alg. Book   | Book          |
| 2          | LC DB. Book    | Book          |
| 3          | LC SmarthPhone | Phone         |
| 4          | LC Phone 2020  | Phone         |
| 5          | LC SmartGlass  | Glasses       |
| 6          | LC T-Shirt XL  | T-Shirt       |
+------------+----------------+---------------+

Result table:
+------------+-----------+-----------+-----------+-----------+-----------+-----------+-----------+
| Category   | Monday    | Tuesday   | Wednesday | Thursday  | Friday    | Saturday  | Sunday    |
+------------+-----------+-----------+-----------+-----------+-----------+-----------+-----------+
| Book       | 20        | 5         | 0         | 0         | 10        | 0         | 0         |
| Glasses    | 0         | 0         | 0         | 0         | 5         | 0         | 0         |
| Phone      | 0         | 0         | 5         | 1         | 0         | 0         | 10        |
| T-Shirt    | 0         | 0         | 0         | 0         | 0         | 0         | 0         |
+------------+-----------+-----------+-----------+-----------+-----------+-----------+-----------+
```

You are the business owner and would like to obtain a sales report for category items and day of the week.

Write an SQL query to report how many units in each category have been ordered on each day of the week.

Return the result table ordered by category.

The query result format is in the following example:

## SQL Answer

```sql
SELECT 
  i.item_category AS CATEGORY,
  SUM(CASE WHEN DAYNAME(o.order_date) = 'Monday' THEN o.quantity ELSE 0 END) AS 'MONDAY',
  SUM(CASE WHEN DAYNAME(o.order_date) = 'Tuesday' THEN o.quantity ELSE 0 END) AS 'TUESDAY',
  SUM(CASE WHEN DAYNAME(o.order_date) = 'Wednesday' THEN o.quantity ELSE 0 END) AS 'WEDNESDAY',
  SUM(CASE WHEN DAYNAME(o.order_date) = 'Thursday' THEN o.quantity ELSE 0 END) AS 'THURSDAY',
  SUM(CASE WHEN DAYNAME(o.order_date) = 'Friday' THEN o.quantity ELSE 0 END) AS 'FRIDAY',
  SUM(CASE WHEN DAYNAME(o.order_date) = 'Saturday' THEN o.quantity ELSE 0 END) AS 'SATURDAY',
  SUM(CASE WHEN DAYNAME(o.order_date) = 'Sunday' THEN o.quantity ELSE 0 END) AS 'SUNDAY'
FROM Items i
LEFT JOIN Orders o
ON o.item_id = i.item_id
GROUP BY i.item_category
ORDER BY i.item_category
```

```
+----------+--------+---------+-----------+----------+--------+----------+--------+
| CATEGORY | MONDAY | TUESDAY | WEDNESDAY | THURSDAY | FRIDAY | SATURDAY | SUNDAY |
+----------+--------+---------+-----------+----------+--------+----------+--------+
| Book     |     20 |       5 |         0 |        0 |     10 |        0 |      0 |
| Glasses  |      0 |       0 |         0 |        0 |      5 |        0 |      0 |
| Phone    |      0 |       0 |         5 |        1 |      0 |        0 |     10 |
| T-Shirt  |      0 |       0 |         0 |        0 |      0 |        0 |      0 |
+----------+--------+---------+-----------+----------+--------+----------+--------+
4 rows in set (0.00 sec)
```

