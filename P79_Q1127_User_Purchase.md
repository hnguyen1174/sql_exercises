## 1. SQL Schema

```sql
Create table If Not Exists Spending (user_id int, spend_date date, platform ENUM('desktop', 'mobile'), amount int);
Truncate table Spending;
insert into Spending (user_id, spend_date, platform, amount) values ('1', '2019-07-01', 'mobile', '100');
insert into Spending (user_id, spend_date, platform, amount) values ('1', '2019-07-01', 'desktop', '100');
insert into Spending (user_id, spend_date, platform, amount) values ('2', '2019-07-01', 'mobile', '100');
insert into Spending (user_id, spend_date, platform, amount) values ('2', '2019-07-02', 'mobile', '100');
insert into Spending (user_id, spend_date, platform, amount) values ('3', '2019-07-01', 'desktop', '100');
insert into Spending (user_id, spend_date, platform, amount) values ('3', '2019-07-02', 'desktop', '100');
```

```
Write a solution to find the total number of users and the total amount spent using the mobile only, the desktop only, and both mobile and desktop together for each date.

Return the result table in any order.

The result format is in the following example.

 

Example 1:

Input: 
Spending table:
+---------+------------+----------+--------+
| user_id | spend_date | platform | amount |
+---------+------------+----------+--------+
| 1       | 2019-07-01 | mobile   | 100    |
| 1       | 2019-07-01 | desktop  | 100    |
| 2       | 2019-07-01 | mobile   | 100    |
| 2       | 2019-07-02 | mobile   | 100    |
| 3       | 2019-07-01 | desktop  | 100    |
| 3       | 2019-07-02 | desktop  | 100    |
+---------+------------+----------+--------+
Output: 
+------------+----------+--------------+-------------+
| spend_date | platform | total_amount | total_users |
+------------+----------+--------------+-------------+
| 2019-07-01 | desktop  | 100          | 1           |
| 2019-07-01 | mobile   | 100          | 1           |
| 2019-07-01 | both     | 200          | 1           |
| 2019-07-02 | desktop  | 100          | 1           |
| 2019-07-02 | mobile   | 100          | 1           |
| 2019-07-02 | both     | 0            | 0           |
+------------+----------+--------------+-------------+ 
Explanation: 
On 2019-07-01, user 1 purchased using both desktop and mobile, user 2 purchased using mobile only and user 3 purchased using desktop only.
On 2019-07-02, user 2 purchased using mobile only, user 3 purchased using desktop only and no one purchased using both platforms.
```

## 2. SQL Answer

```sql
WITH mobile_desktop_amount AS (
  SELECT
    spend_date,
    user_id,
    SUM(CASE platform WHEN 'mobile' THEN amount ELSE 0 END) mobile_amount,
    SUM(CASE platform WHEN 'desktop' THEN amount ELSE 0 END) desktop_amount
  FROM Spending
  GROUP BY spend_date, user_id
),

platform AS (
  SELECT
    spend_date,
    user_id,
    IF(
      mobile_amount > 0, IF(desktop_amount > 0, 'both', 'mobile'), 
      'desktop') AS platform,
    (mobile_amount + desktop_amount) AS amount
  FROM mobile_desktop_amount
),

all_spend_date AS (
  SELECT DISTINCT(spend_date), 'desktop' platform FROM Spending
  UNION
  SELECT DISTINCT(spend_date), 'mobile' platform FROM Spending
  UNION
  SELECT DISTINCT(spend_date), 'both' platform FROM Spending 
)

SELECT 
    p.spend_date,
    p.platform,
    IFNULL(SUM(amount), 0) AS total_amount,
    COUNT(user_id) total_users
FROM all_spend_date p
LEFT JOIN platform t
  ON p.platform = t.platform 
  AND p.spend_date = t.spend_date
GROUP BY spend_date, platform
;
```