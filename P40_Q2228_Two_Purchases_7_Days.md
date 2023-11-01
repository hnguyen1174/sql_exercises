## 1. Table and Schema

```sql
Create table If Not Exists Purchases (purchase_id int, user_id int, purchase_date date);
Truncate table Purchases;
insert into Purchases (purchase_id, user_id, purchase_date) values ('4', '2', '2022-03-13');
insert into Purchases (purchase_id, user_id, purchase_date) values ('1', '5', '2022-02-11');
insert into Purchases (purchase_id, user_id, purchase_date) values ('3', '7', '2022-06-19');
insert into Purchases (purchase_id, user_id, purchase_date) values ('6', '2', '2022-03-20');
insert into Purchases (purchase_id, user_id, purchase_date) values ('5', '7', '2022-06-19');
insert into Purchases (purchase_id, user_id, purchase_date) values ('2', '2', '2022-06-08');
```

```
Write a solution to report the IDs of the users that made any two purchases at most 7 days apart.

Return the result table ordered by user_id.

The result format is in the following example.

 

Example 1:

Input: 
Purchases table:
+-------------+---------+---------------+
| purchase_id | user_id | purchase_date |
+-------------+---------+---------------+
| 4           | 2       | 2022-03-13    |
| 1           | 5       | 2022-02-11    |
| 3           | 7       | 2022-06-19    |
| 6           | 2       | 2022-03-20    |
| 5           | 7       | 2022-06-19    |
| 2           | 2       | 2022-06-08    |
+-------------+---------+---------------+
Output: 
+---------+
| user_id |
+---------+
| 2       |
| 7       |
+---------+
Explanation: 
User 2 had two purchases on 2022-03-13 and 2022-03-20. Since the second purchase is within 7 days of the first purchase, we add their ID.
User 5 had only 1 purchase.
User 7 had two purchases on the same day so we add their ID.
```

## 2. SQL Answer

```sql
WITH cte AS (
  SELECT
    *,
    ROW_NUMBER() OVER () AS rn
  FROM Purchases
)

SELECT DISTINCT
  p1.user_id
FROM cte p1
JOIN cte p2
  ON p1.user_id = p2.user_id
  AND ABS(DATEDIFF(p2.purchase_date, p1.purchase_date)) <= 7
  AND p1.rn <> p2.rn
ORDER BY 1 ASC
;
```