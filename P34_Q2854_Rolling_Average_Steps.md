## Table and Schema

```sql
Create table if not exists Steps(user_id int, steps_count int, steps_date date);
Truncate table Steps;
insert into Steps (user_id, steps_count, steps_date) values ('1', '687', '2021-09-02');
insert into Steps (user_id, steps_count, steps_date) values ('1', '395', '2021-09-04');
insert into Steps (user_id, steps_count, steps_date) values ('1', '499', '2021-09-05');
insert into Steps (user_id, steps_count, steps_date) values ('1', '712', '2021-09-06');
insert into Steps (user_id, steps_count, steps_date) values ('1', '576', '2021-09-07');
insert into Steps (user_id, steps_count, steps_date) values ('2', '153', '2021-09-06');
insert into Steps (user_id, steps_count, steps_date) values ('2', '171', '2021-09-07');
insert into Steps (user_id, steps_count, steps_date) values ('2', '530', '2021-09-08');
insert into Steps (user_id, steps_count, steps_date) values ('3', '945', '2021-09-04');
insert into Steps (user_id, steps_count, steps_date) values ('3', '120', '2021-09-07');
insert into Steps (user_id, steps_count, steps_date) values ('3', '557', '2021-09-08');
insert into Steps (user_id, steps_count, steps_date) values ('3', '840', '2021-09-09');
insert into Steps (user_id, steps_count, steps_date) values ('3', '627', '2021-09-10');
insert into Steps (user_id, steps_count, steps_date) values ('5', '382', '2021-09-05');
insert into Steps (user_id, steps_count, steps_date) values ('6', '480', '2021-09-01');
insert into Steps (user_id, steps_count, steps_date) values ('6', '191', '2021-09-02');
insert into Steps (user_id, steps_count, steps_date) values ('6', '303', '2021-09-05')
;
```

```
Write a solution to calculate 3-day rolling averages of steps for each user.

We calculate the n-day rolling average this way:

For each day, we calculate the average of n consecutive days of step counts ending on that day if available, otherwise, n-day rolling average is not defined for it.
Output the user_id, steps_date, and rolling average. Round the rolling average to two decimal places.

Return the result table ordered by user_id, steps_date in ascending order.

The result format is in the following example.

Input: 
Steps table:
+---------+-------------+------------+
| user_id | steps_count | steps_date |
+---------+-------------+------------+
| 1       | 687         | 2021-09-02 |
| 1       | 395         | 2021-09-04 |
| 1       | 499         | 2021-09-05 |
| 1       | 712         | 2021-09-06 |
| 1       | 576         | 2021-09-07 |
| 2       | 153         | 2021-09-06 |
| 2       | 171         | 2021-09-07 |
| 2       | 530         | 2021-09-08 |
| 3       | 945         | 2021-09-04 |
| 3       | 120         | 2021-09-07 |
| 3       | 557         | 2021-09-08 |
| 3       | 840         | 2021-09-09 |
| 3       | 627         | 2021-09-10 |
| 5       | 382         | 2021-09-05 |
| 6       | 480         | 2021-09-01 |
| 6       | 191         | 2021-09-02 |
| 6       | 303         | 2021-09-05 |
+---------+-------------+------------+

Output: 
+---------+------------+-----------------+
| user_id | steps_date | rolling_average | 
+---------+------------+-----------------+
| 1       | 2021-09-06 | 535.33          | 
| 1       | 2021-09-07 | 595.67          | 
| 2       | 2021-09-08 | 284.67          |
| 3       | 2021-09-09 | 505.67          |
| 3       | 2021-09-10 | 674.67          |    
+---------+------------+-----------------+
```

## SQL Answers

```sql
-- Step 1: Find user, date pairs on which there are 2 consecutive dates before it
-- Step 2: Perform rolling average on these dates

SELECT
  s1.user_id,
  s1.steps_date,
  ROUND((s1.steps_count + s2.steps_count + s3.steps_count) / 3.0, 2) AS rolling_average
FROM Steps s1
JOIN Steps s2
  ON DATEDIFF(s1.steps_date, s2.steps_date) = 1
  AND s1.user_id = s2.user_id
JOIN Steps s3
  ON DATEDIFF(s2.steps_date, s3.steps_date) = 1
  AND s2.user_id = s3.user_id
ORDER BY s1.user_id, s1.steps_date
;
```