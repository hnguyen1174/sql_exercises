## 1. Table Schema


```sql
Create table If Not Exists Contests (contest_id int, gold_medal int, silver_medal int, bronze_medal int);
Create table If Not Exists Users (user_id int, mail varchar(50), name varchar(30));
Truncate table Contests;
insert into Contests (contest_id, gold_medal, silver_medal, bronze_medal) values ('190', '1', '5', '2');
insert into Contests (contest_id, gold_medal, silver_medal, bronze_medal) values ('191', '2', '3', '5');
insert into Contests (contest_id, gold_medal, silver_medal, bronze_medal) values ('192', '5', '2', '3');
insert into Contests (contest_id, gold_medal, silver_medal, bronze_medal) values ('193', '1', '3', '5');
insert into Contests (contest_id, gold_medal, silver_medal, bronze_medal) values ('194', '4', '5', '2');
insert into Contests (contest_id, gold_medal, silver_medal, bronze_medal) values ('195', '4', '2', '1');
insert into Contests (contest_id, gold_medal, silver_medal, bronze_medal) values ('196', '1', '5', '2');
Truncate table Users;
insert into Users (user_id, mail, name) values ('1', 'sarah@leetcode.com', 'Sarah');
insert into Users (user_id, mail, name) values ('2', 'bob@leetcode.com', 'Bob');
insert into Users (user_id, mail, name) values ('3', 'alice@leetcode.com', 'Alice');
insert into Users (user_id, mail, name) values ('4', 'hercy@leetcode.com', 'Hercy');
insert into Users (user_id, mail, name) values ('5', 'quarz@leetcode.com', 'Quarz');
```

```
Write a solution to report the name and the mail of all interview candidates. A user is an interview candidate if at least one of these two conditions is true:

The user won any medal in three or more consecutive contests.
The user won the gold medal in three or more different contests (not necessarily consecutive).
Return the result table in any order.

The result format is in the following example.

 

Example 1:

Input: 
Contests table:
+------------+------------+--------------+--------------+
| contest_id | gold_medal | silver_medal | bronze_medal |
+------------+------------+--------------+--------------+
| 190        | 1          | 5            | 2            |
| 191        | 2          | 3            | 5            |
| 192        | 5          | 2            | 3            |
| 193        | 1          | 3            | 5            |
| 194        | 4          | 5            | 2            |
| 195        | 4          | 2            | 1            |
| 196        | 1          | 5            | 2            |
+------------+------------+--------------+--------------+
Users table:
+---------+--------------------+-------+
| user_id | mail               | name  |
+---------+--------------------+-------+
| 1       | sarah@leetcode.com | Sarah |
| 2       | bob@leetcode.com   | Bob   |
| 3       | alice@leetcode.com | Alice |
| 4       | hercy@leetcode.com | Hercy |
| 5       | quarz@leetcode.com | Quarz |
+---------+--------------------+-------+
Output: 
+-------+--------------------+
| name  | mail               |
+-------+--------------------+
| Sarah | sarah@leetcode.com |
| Bob   | bob@leetcode.com   |
| Alice | alice@leetcode.com |
| Quarz | quarz@leetcode.com |
+-------+--------------------+
Explanation: 
Sarah won 3 gold medals (190, 193, and 196), so we include her in the result table.
Bob won a medal in 3 consecutive contests (190, 191, and 192), so we include him in the result table.
    - Note that he also won a medal in 3 other consecutive contests (194, 195, and 196).
Alice won a medal in 3 consecutive contests (191, 192, and 193), so we include her in the result table.
Quarz won a medal in 5 consecutive contests (190, 191, 192, 193, and 194), so we include them in the result table.
```

## 2. SQL Answer

```sql
WITH win_gold AS (
  SELECT
    gold_medal AS user_id
  FROM Contests
  GROUP BY 1
  HAVING COUNT(*) >= 3
),

any_contest AS (
  SELECT gold_medal AS user_id, contest_id FROM Contests
  UNION 
  SELECT silver_medal AS user_id, contest_id FROM Contests
  UNION 
  SELECT bronze_medal AS user_id, contest_id FROM Contests
),

win_any_contest AS (
  SELECT DISTINCt
    t1.user_id
  FROM any_contest t1
  JOIN any_contest t2
    ON t1.user_id = t2.user_id
    AND t1.contest_id + 1 = t2.contest_id
  JOIN any_contest t3
    ON t2.user_id = t3.user_id
    AND t2.contest_id + 1 = t3.contest_id
)

SELECT
  u.name,
  u.mail
FROM (
  SELECT user_id FROM win_gold
  UNION 
  SELECT user_id FROM win_any_contest
)  w
LEFT JOIN Users u
  ON w.user_id = u.user_id
;
```