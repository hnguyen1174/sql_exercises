## 1. Table Schema

```sql
Create table If Not Exists Members (member_id int, name varchar(30));
Create table If Not Exists Visits (visit_id int, member_id int, visit_date date);
Create table If Not Exists Purchases (visit_id int, charged_amount int);
Truncate table Members;
insert into Members (member_id, name) values ('9', 'Alice');
insert into Members (member_id, name) values ('11', 'Bob');
insert into Members (member_id, name) values ('3', 'Winston');
insert into Members (member_id, name) values ('8', 'Hercy');
insert into Members (member_id, name) values ('1', 'Narihan');
Truncate table Visits;
insert into Visits (visit_id, member_id, visit_date) values ('22', '11', '2021-10-28');
insert into Visits (visit_id, member_id, visit_date) values ('16', '11', '2021-01-12');
insert into Visits (visit_id, member_id, visit_date) values ('18', '9', '2021-12-10');
insert into Visits (visit_id, member_id, visit_date) values ('19', '3', '2021-10-19');
insert into Visits (visit_id, member_id, visit_date) values ('12', '11', '2021-03-01');
insert into Visits (visit_id, member_id, visit_date) values ('17', '8', '2021-05-07');
insert into Visits (visit_id, member_id, visit_date) values ('21', '9', '2021-05-12');
Truncate table Purchases;
insert into Purchases (visit_id, charged_amount) values ('12', '2000');
insert into Purchases (visit_id, charged_amount) values ('18', '9000');
insert into Purchases (visit_id, charged_amount) values ('17', '7000');
```

```
A store wants to categorize its members. There are three tiers:

"Diamond": if the conversion rate is greater than or equal to 80.
"Gold": if the conversion rate is greater than or equal to 50 and less than 80.
"Silver": if the conversion rate is less than 50.
"Bronze": if the member never visited the store.
The conversion rate of a member is (100 * total number of purchases for the member) / total number of visits for the member.

Write a solution to report the id, the name, and the category of each member.

Return the result table in any order.

The result format is in the following example.

 

Example 1:

Input: 
Members table:
+-----------+---------+
| member_id | name    |
+-----------+---------+
| 9         | Alice   |
| 11        | Bob     |
| 3         | Winston |
| 8         | Hercy   |
| 1         | Narihan |
+-----------+---------+
Visits table:
+----------+-----------+------------+
| visit_id | member_id | visit_date |
+----------+-----------+------------+
| 22       | 11        | 2021-10-28 |
| 16       | 11        | 2021-01-12 |
| 18       | 9         | 2021-12-10 |
| 19       | 3         | 2021-10-19 |
| 12       | 11        | 2021-03-01 |
| 17       | 8         | 2021-05-07 |
| 21       | 9         | 2021-05-12 |
+----------+-----------+------------+
Purchases table:
+----------+----------------+
| visit_id | charged_amount |
+----------+----------------+
| 12       | 2000           |
| 18       | 9000           |
| 17       | 7000           |
+----------+----------------+
Output: 
+-----------+---------+----------+
| member_id | name    | category |
+-----------+---------+----------+
| 1         | Narihan | Bronze   |
| 3         | Winston | Silver   |
| 8         | Hercy   | Diamond  |
| 9         | Alice   | Gold     |
| 11        | Bob     | Silver   |
+-----------+---------+----------+
Explanation: 
- User Narihan with id = 1 did not make any visits to the store. She gets a Bronze category.
- User Winston with id = 3 visited the store one time and did not purchase anything. The conversion rate = (100 * 0) / 1 = 0. He gets a Silver category.
- User Hercy with id = 8 visited the store one time and purchased one time. The conversion rate = (100 * 1) / 1 = 1. He gets a Diamond category.
- User Alice with id = 9 visited the store two times and purchased one time. The conversion rate = (100 * 1) / 2 = 50. She gets a Gold category.
- User Bob with id = 11 visited the store three times and purchased one time. The conversion rate = (100 * 1) / 3 = 33.33. He gets a Silver category.
```

## 2. SQL Answer

```sql
WITH cte AS (
  SELECT
    m.*,
    v.visit_id,
    v.visit_date,
    CASE WHEN COALESCE(p.charged_amount, 0) > 0 THEN 1 ELSE 0 END AS purchase
  FROM Members m
  LEFT JOIN Visits v
    ON m.member_id = v.member_id
  LEFT JOIN Purchases p
    ON v.visit_id = p.visit_id
),

final AS (
  SELECT
    member_id,
    name,
    COUNT(DISTINCT visit_id) AS num_visit,
    SUM(purchase) AS num_purchase,
    SUM(purchase) * 100.0 / COUNT(DISTINCT visit_id) AS conversion_rate
  FROM cte
  GROUP BY 1,2
)

SELECT
  member_id,
  name,
  CASE
    WHEN conversion_rate >= 80 THEN 'Diamond'
    WHEN conversion_rate >= 50 AND conversion_rate < 80 THEN 'Gold'
    WHEN conversion_rate IS NULL THEN 'Bronze'
    ELSE 'Silver'
  END AS category
FROM final
;
```