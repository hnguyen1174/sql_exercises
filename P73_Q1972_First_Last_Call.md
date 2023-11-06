## 1. SQL Schema

```sql
Create table If Not Exists Calls (caller_id int, recipient_id int, call_time datetime);
Truncate table Calls;
insert into Calls (caller_id, recipient_id, call_time) values ('8', '4', '2021-08-24 17:46:07');
insert into Calls (caller_id, recipient_id, call_time) values ('4', '8', '2021-08-24 19:57:13');
insert into Calls (caller_id, recipient_id, call_time) values ('5', '1', '2021-08-11 05:28:44');
insert into Calls (caller_id, recipient_id, call_time) values ('8', '3', '2021-08-17 04:04:15');
insert into Calls (caller_id, recipient_id, call_time) values ('11', '3', '2021-08-17 13:07:00');
insert into Calls (caller_id, recipient_id, call_time) values ('8', '11', '2021-08-17 22:22:22');
```

```
Write a solution to report the IDs of the users whose first and last calls on any day were with the same person. Calls are counted regardless of being the caller or the recipient.

Return the result table in any order.

The result format is in the following example.

 

Example 1:

Input: 
Calls table:
+-----------+--------------+---------------------+
| caller_id | recipient_id | call_time           |
+-----------+--------------+---------------------+
| 8         | 4            | 2021-08-24 17:46:07 |
| 4         | 8            | 2021-08-24 19:57:13 |
| 5         | 1            | 2021-08-11 05:28:44 |
| 8         | 3            | 2021-08-17 04:04:15 |
| 11        | 3            | 2021-08-17 13:07:00 |
| 8         | 11           | 2021-08-17 22:22:22 |
+-----------+--------------+---------------------+
Output: 
+---------+
| user_id |
+---------+
| 1       |
| 4       |
| 5       |
| 8       |
+---------+
Explanation: 
On 2021-08-24, the first and last call of this day for user 8 was with user 4. User 8 should be included in the answer.
Similarly, user 4 on 2021-08-24 had their first and last call with user 8. User 4 should be included in the answer.
On 2021-08-11, user 1 and 5 had a call. This call was the only call for both of them on this day. Since this call is the first and last call of the day for both of them, they should both be included in the answer.
```

## 2. SQL Answer

```sql
WITH all_calls AS (
  SELECT * FROM Calls
  UNION
  SELECT recipient_id AS caller_id, caller_id AS recipient_id, call_time FROM Calls
),

first_last AS (
  SELECT
    *,
    ROW_NUMBER() OVER (PARTITION BY caller_id, DATE(call_time) ORDER BY call_time ASC) AS first_call,
    ROW_NUMBER() OVER (PARTITION BY caller_id, DATE(call_time) ORDER BY call_time DESC) AS last_call
  FROM all_calls
),

one_call AS (
  SELECT DISTINCT caller_id FROM first_last WHERE first_call = last_call
  AND first_call = 1
),

more_than_one_call AS (
  SELECT 
    caller_id
  FROM first_last
  WHERE first_call = 1
  OR last_call = 1
  GROUP BY caller_id, recipient_id
  HAVING COUNT(*) = 2
)

SELECT caller_id AS user_id FROM one_call
UNION
SELECT caller_id AS user_id FROM more_than_one_call
;
```