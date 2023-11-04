## 1. Table Schema

```sql
Create table If Not Exists Friends (id int, name varchar(30), activity varchar(30));
Create table If Not Exists Activities (id int, name varchar(30));
Truncate table Friends;
insert into Friends (id, name, activity) values ('1', 'Jonathan D.', 'Eating');
insert into Friends (id, name, activity) values ('2', 'Jade W.', 'Singing');
insert into Friends (id, name, activity) values ('3', 'Victor J.', 'Singing');
insert into Friends (id, name, activity) values ('4', 'Elvis Q.', 'Eating');
insert into Friends (id, name, activity) values ('5', 'Daniel A.', 'Eating');
insert into Friends (id, name, activity) values ('6', 'Bob B.', 'Horse Riding');
Truncate table Activities;
insert into Activities (id, name) values ('1', 'Eating');
insert into Activities (id, name) values ('2', 'Singing');
insert into Activities (id, name) values ('3', 'Horse Riding');
```

```
Find the names of all the activities with neither the maximum nor the minimum number of participants.

Each activity in the Activities table is performed by any person in the table Friends.

Return the result table in any order.

The result format is in the following example.

 

Example 1:

Input: 
Friends table:
+------+--------------+---------------+
| id   | name         | activity      |
+------+--------------+---------------+
| 1    | Jonathan D.  | Eating        |
| 2    | Jade W.      | Singing       |
| 3    | Victor J.    | Singing       |
| 4    | Elvis Q.     | Eating        |
| 5    | Daniel A.    | Eating        |
| 6    | Bob B.       | Horse Riding  |
+------+--------------+---------------+
Activities table:
+------+--------------+
| id   | name         |
+------+--------------+
| 1    | Eating       |
| 2    | Singing      |
| 3    | Horse Riding |
+------+--------------+
Output: 
+--------------+
| activity     |
+--------------+
| Singing      |
+--------------+
Explanation: 
Eating activity is performed by 3 friends, maximum number of participants, (Jonathan D. , Elvis Q. and Daniel A.)
Horse Riding activity is performed by 1 friend, minimum number of participants, (Bob B.)
Singing is performed by 2 friends (Victor J. and Jade W.)
```

```sql
WITH act_cnt AS (
  SELECT
    activity,
    COUNT(name) AS num_participant
  FROM Friends
  GROUP BY 1
),

max_min AS (
  SELECT
    MAX(num_participant) AS max_p,
    MIN(num_participant) AS min_p
  FROM act_cnt
)

SELECT activity FROM act_cnt 
WHERE num_participant NOT IN (SELECT max_p FROM max_min) 
AND num_participant NOT IN (SELECT min_p FROM max_min) 
;
```