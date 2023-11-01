## 1. Table Schema


```sql
Create table If Not Exists TeamPoints (team_id int, name varchar(100), points int);
Create table If Not Exists PointsChange (team_id int, points_change int);
Truncate table TeamPoints;
insert into TeamPoints (team_id, name, points) values ('3', 'Algeria', '1431');
insert into TeamPoints (team_id, name, points) values ('1', 'Senegal', '2132');
insert into TeamPoints (team_id, name, points) values ('2', 'New Zealand', '1402');
insert into TeamPoints (team_id, name, points) values ('4', 'Croatia', '1817');
Truncate table PointsChange;
insert into PointsChange (team_id, points_change) values ('3', '399');
insert into PointsChange (team_id, points_change) values ('2', '0');
insert into PointsChange (team_id, points_change) values ('4', '13');
insert into PointsChange (team_id, points_change) values ('1', '-22');
```

```
The global ranking of a national team is its rank after sorting all the teams by their points in descending order. If two teams have the same points, we break the tie by sorting them by their name in lexicographical order.

The points of each national team should be updated based on its corresponding points_change value.

Write a solution to calculate the change in the global rankings after updating each team's points.

Return the result table in any order.

The result format is in the following example.

 

Example 1:

Input: 
TeamPoints table:
+---------+-------------+--------+
| team_id | name        | points |
+---------+-------------+--------+
| 3       | Algeria     | 1431   |
| 1       | Senegal     | 2132   |
| 2       | New Zealand | 1402   |
| 4       | Croatia     | 1817   |
+---------+-------------+--------+
PointsChange table:
+---------+---------------+
| team_id | points_change |
+---------+---------------+
| 3       | 399           |
| 2       | 0             |
| 4       | 13            |
| 1       | -22           |
+---------+---------------+
Output: 
+---------+-------------+-----------+
| team_id | name        | rank_diff |
+---------+-------------+-----------+
| 1       | Senegal     | 0         |
| 4       | Croatia     | -1        |
| 3       | Algeria     | 1         |
| 2       | New Zealand | 0         |
+---------+-------------+-----------+
Explanation: 
The global rankings were as follows:
+---------+-------------+--------+------+
| team_id | name        | points | rank |
+---------+-------------+--------+------+
| 1       | Senegal     | 2132   | 1    |
| 4       | Croatia     | 1817   | 2    |
| 3       | Algeria     | 1431   | 3    |
| 2       | New Zealand | 1402   | 4    |
+---------+-------------+--------+------+
After updating the points of each team, the rankings became the following:
+---------+-------------+--------+------+
| team_id | name        | points | rank |
+---------+-------------+--------+------+
| 1       | Senegal     | 2110   | 1    |
| 3       | Algeria     | 1830   | 2    |
| 4       | Croatia     | 1830   | 3    |
| 2       | New Zealand | 1402   | 4    |
+---------+-------------+--------+------+
Since after updating the points Algeria and Croatia have the same points, they are ranked according to their lexicographic order.
Senegal lost 22 points but their rank did not change.
Croatia gained 13 points but their rank decreased by one.
Algeria gained 399 points and their rank increased by one.
New Zealand did not gain or lose points and their rank did not change.
```

## 2. SQL Answer

```sql
WITH original AS (
  SELECT
    team_id,
    name,
    RANK() OVER (ORDER BY points DESC, name ASC) AS rn
  FROM TeamPoints
),

new_points AS (
  SELECT
    t.team_id,
    t.name,
    RANK() OVER (ORDER BY t.points+p.points_change DESC, name ASC) AS rn
  FROM TeamPoints t
  LEFT JOIN PointsChange p
    ON t.team_id = p.team_id
)

SELECT
  o.team_id,
  o.name,
  CAST(o.rn AS SIGNED) - CAST(n.rn AS SIGNED) AS rank_diff
FROM original o
JOIN new_points n
  ON o.team_id = n.team_id
;
```