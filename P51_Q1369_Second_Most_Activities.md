## 1. Table Schema

```sql
Create table If Not Exists UserActivity (username varchar(30), activity varchar(30), startDate date, endDate date);
Truncate table UserActivity;
insert into UserActivity (username, activity, startDate, endDate) values ('Alice', 'Travel', '2020-02-12', '2020-02-20');
insert into UserActivity (username, activity, startDate, endDate) values ('Alice', 'Dancing', '2020-02-21', '2020-02-23');
insert into UserActivity (username, activity, startDate, endDate) values ('Alice', 'Travel', '2020-02-24', '2020-02-28');
insert into UserActivity (username, activity, startDate, endDate) values ('Bob', 'Travel', '2020-02-11', '2020-02-18');
```

```
Write a solution to show the second most recent activity of each user.

If the user only has one activity, return that one. A user cannot perform more than one activity at the same time.

Return the result table in any order.

The result format is in the following example.

 

Example 1:

Input: 
UserActivity table:
+------------+--------------+-------------+-------------+
| username   | activity     | startDate   | endDate     |
+------------+--------------+-------------+-------------+
| Alice      | Travel       | 2020-02-12  | 2020-02-20  |
| Alice      | Dancing      | 2020-02-21  | 2020-02-23  |
| Alice      | Travel       | 2020-02-24  | 2020-02-28  |
| Bob        | Travel       | 2020-02-11  | 2020-02-18  |
+------------+--------------+-------------+-------------+
Output: 
+------------+--------------+-------------+-------------+
| username   | activity     | startDate   | endDate     |
+------------+--------------+-------------+-------------+
| Alice      | Dancing      | 2020-02-21  | 2020-02-23  |
| Bob        | Travel       | 2020-02-11  | 2020-02-18  |
+------------+--------------+-------------+-------------+
Explanation: 
The most recent activity of Alice is Travel from 2020-02-24 to 2020-02-28, before that she was dancing from 2020-02-21 to 2020-02-23.
Bob only has one record, we just take that one.
```

## 2. SQL Answer

```sql
WITH activity_desc AS (
  SELECT 
    *,
    RANK() OVER (PARTITION BY username ORDER BY startDate DESC) AS rn 
  FROM UserActivity
),

num_activities AS (
  SELECT
    username,
    COUNT(*) AS cnt
  FROM (
    SELECT DISTINCT username, activity, startDate, endDate 
    FROM UserActivity
  ) t
  GROUP BY 1
),

one_activity AS (
  SELECT username FROM num_activities WHERE cnt = 1
)

SELECT username, activity, startDate, endDate FROM activity_desc WHERE rn = 2
UNION
SELECT * FROM UserActivity WHERE username IN (SELECT username FROM one_activity)
;
```

```sql
-- Best Answer
SELECT 
  username, 
  activity, 
  startDate, 
  endDate
FROM (
  SELECT 
    *, 
    COUNT(activity) OVER (PARTITION BY username) AS cnt, 
    ROW_NUMBER() OVER (PARTITION BY username ORDER BY startdate DESC) AS n 
  FROM UserActivity
) tbl
WHERE n = 2 
  OR cnt < 2
```